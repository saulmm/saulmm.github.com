---
layout: post
permalink: when-Iron-Man-becomes-Reactive-Avengers2
title: When Iron Man becomes reactive, RxJava
---

This part of the series is focused on the benefits that some funcional touches can provide a to our projects. 

A framework like `RxJava` of [ReactiveX] can easily help to handle different running environments running on tasks in background or in the UI thread. On android that has always been a nightmare for all us.

This article also focuses on how the operators can minimize the time in common development tasks, [Reactive Extensions](http://reactivex.io/) offers a wide range of operators to make your live easier.

As always, most of the code and snippets are uploaded to Github, feel free to comment, open an issue or complaining!. 

[Avengers app on Github](https://github.com/saulmm/Avengers)

In the [first part](http://saulmm.github.io/when-Thor-and-Hulk-meet-dagger2-rxjava-1/) of this series we talked about [Dagger 2](http://google.github.io/dagger/), as we go forward we'll see as the coupling between our layers will be less and increase scalability.

<br>
## RetroLambda

Sometimes, in huge applications developed in Java, or large frameworks like android, it's really difficult or even impossible (android) to use features from Java 8 like the [Lambda expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html).

[Retrolambda] it's a _Backport_ that comes to solve this problem, translating the Java 8 bytecode to previous Java versions like the v7, or even v5 or v6, allowing us to use _Lambda_ among other features.

![](http://androcode.es/wp-content/uploads/2015/06/retrolambda.png)

[Retrolambda] can be used with the Gradle plugin or Maven, I've choosen Gradle that comes for free in Android Studio, to use it you only have to add the [Retrolambda] plugin on the root `build.gradle` and apply it in your module `build.gradle`, setting the language level of Android Studio to **1.8** all it's done.

_build.gradle_ (root)

```javascript
    dependencies {
        classpath 'me.tatarka:gradle-retrolambda:3.1.0'
```

_{your module}/build.gradle_

```javascript

    apply plugin: 'me.tatarka.retrolambda'

    android { 

        ...

        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }
}
```

[RetroLambda] allows you to write less boilerplate code, also clarifies our code making it more readable. In this example by [Dan Lew](http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/), you can taste the diference.

_Without RetroLambda_

```java
Observable.just("Hello, world!")
    .subscribe(new Action1<String>() {
        @Override
        public void call(String s) {
              System.out.println(s);
        }
    });
```

_With RetroLambda_

```java
Observable.just("Hello, world!") .subscribe(
    s -> System.out.println(s)
);
```

_In the Avengers example_

```java
mCharacterSubscription = mGetCharacterInformationUsecase
    .execute().subscribe(
        character -> onAvengerReceived(character),
        error     -> manageError(error)
    );

mComicsSubscription = mGetCharacterComicsUsecase
    .execute().subscribe(
        comics -> Observable.from(comics).subscribe(
            comic -> onComicReceived(comic)),
        error  -> manageError(throwable)
    );
```

<br>
## ReactiveX

[ReactiveX](http://reactivex.io/) is a collection of Open Source projects among their main principles are the [Observer](http://en.wikipedia.org/wiki/Observer_pattern) pattern, the pattern [Iterator](http://en.wikipedia.org/wiki/Iterator_pattern#Java) and the functional programming.

[ReactiveX](http://reactivex.io/) it's also defined as an API for asynchronous programming, in fact it's really easy to implement asynchronous task with these frameworks.

<br>
## ReactiveX as an asynchronous client

The great thing about dealing with [ReactiveX] is that you can create a fully asynchronous API or client, and then in the implementation decide whether the code will be treated asynchronously or in a separate `Thread` a `Threadpool` or synchronously.

So we have an observable API rather than a blocking API.

```java
public interface Usecase<T> {

    Observable<T> execute();
}
```


```java
public interface Repository {

    Observable<Character> getCharacter (final int characterId);

    Observable<List<Comic>> getCharacterComics (final int characterId);
}
```

<br>
## What is RxJava

[RxJava](https://github.com/ReactiveX/RxJava) is an implementation of the [Reactive Extensions](https://github.com/ReactiveX) made by **Netflix**. There are implementations for the vast majority of programming languages including Javascript, Python, Ruby, Go and many more.

<br>
## Observables & Observers

An `Observable` emits an object or series of objects, these objects are consumed or received by the `Observer` subscribed to the 'Observable'.
<br>

![](http://androcode.es/wp-content/uploads/2015/06/observable-observer2.png)
<br>
Is necessary that an `Observer` has to be registered into an `Observable`, if not the `Observer` won't emit anything. When the `Observer` is registered, an object of the type `Subscription` is created, which is used to unsubscribe from the `Observable`, this is useful for `Activities` and `Fragments` on the `onStop` or `onPause` methods, for example.

```java
mCharacterSubscription = mGetCharacterInformationUsecase
    .execute().subscribe( ... );
            
```


```java
@Override
public void onStop() {

    if (!mCharacterSubscription.isUnsubscribed())
        mCharacterSubscription.unsubscribe();

    if (!mComicsSubscription.isUnsubscribed())
        mComicsSubscription.unsubscribe();
}
```

Whenever an `Observer` subscribes to an `Observable` has to take into account three methods.

- `onNext (T)` method to receive objects issued by the 'Observable'
- `onError (Exception)`, the method that will be called when there is some kind of error
- `onCompleted()`, method to be called when the `Observable` finished emitting objects.

<br>
_I love this image :)_
![](http://androcode.es/wp-content/uploads/2015/06/observable_observer.png)

<br>
## Communicating components

Let's take a look at how to use the `GetCharacterInformationUsecase` usecase.
All usecases implement the interface `Usecase <T>`:

```java
public interface Usecase<T> {

    Observable<T> execute();
}
```

When a usecase is run this return an object of the type `Observable`, this is useful to be able to chain observables & operators with the least effort, we will see the great power that these operators soon.

When we run the `GetCharacterInformationUsecase` we say to our repository to make a request to our corresponding data source:

```java
@Override
public Observable<Character> execute() {

    return mRepository.getCharacter(mCharacterId);
        // .awesomeRxStuff();
}
```

The presenter `AvengerDetailPresenter` will be our `Observer` for this usecase will be who subscribes to the events sent by the `Observable`, this is done through the method `subscribe`, which connects the `Observer` with the `Observable`.

`onNext` and `onError` methods are implemented to manage the results of the operation. The `onCompleted` method is not implemented in this case it's not necessary.

```
    mCharacterSubscription = mGetCharacterInformationUsecase
        .execute().subscribe(
            character   -> onAvengerReceived(character),
            error       -> manageError(error));
```

<br>
## Retrofit & RxJava

[Retrofit](https://github.com/square/retrofit) from [Square](http://square.github.io/), [RxJava](https://github.com/ReactiveX/RxJava) supports methods of the type `rx. Observable` so the requests can be observed using `Observers` and modified and transformed by operators.

You must be aware of where are you calling it, [Retrofit](https://github.com/square/retrofit) executes the requests in the thread where your `Observable` leaves, so if you call it from the UI thread (an activity or Fragment) would get an error. Let's talk about `Schedulers`!.

## Schedulers

The [http://reactivex.io/documentation/scheduler.html](Schedulers) allow to use _multhreading_ between operators and `Observables`. This can be used with  different threads, a Thread Executor, or preset [Schedulers](http://reactivex.io/documentation/scheduler.html), for example, for input and output operations exists the `Schedulers.io ()`.

[RxAndroid](https://github.com/ReactiveX/RxAndroid) are a few specific android utilities for [RxJava](https://github.com/ReactiveX/RxJava) by [Jake Wharton](http://jakewharton.com/) & [Matthias Käppler](https://plus.google.com/u/0/+MatthiasK%C3%A4ppler/posts), which includes some [Schedulers](http://reactivex.io/documentation/scheduler.html) to manage the threads of the Android platform. 

It also provides the possibility of using an android `Handler` for concurrency management.

```java
    @Override
    public Observable<Character> execute() {

        return mRepository.getCharacter(mCharacterId)
            .subscribeOn(Schedulers.newThread())
            .observeOn(AndroidSchedulers.mainThread());
    }
```

This example demonstrates the ease provided by Rx for managing threads in Android, which has always been a terror to all :D

## Operators

[ReactiveX] great power lies in the operators, they allow to manipulate, transform and combine objects issued by the `Observables`.

Let's think about a list of comics of a character, comics have a specific year, and we want to show the comics to a given year. [ReactiveX] comes to help us!

This filtering is done use of operator [filter](http://reactivex.io/documentation/operators/filter.html), which allows using a predicate to discriminate between the comics, in such a way, ask the user why year wants to filter and that year uses predicate to display comics to date.


```java
public Observable<Comic> filterByYear(String year) {

    if (mComics != null) {
        return Observable.from(mComics).filter(
            comic -> {
                for (ComicDate comicDate : comic.getDates())
                    if (comicDate.getDate().startsWith(year))
                        return true;

                return false;
        });

    }

    return null;
}
```


### Error Handling

Another great example of how the Rx operators can save us some time and give us productivity are the [error handling](https://github.com/ReactiveX/RxJava/wiki/Error-Handling-Operators) Operators.

Imagine that a user makes a request to the network, and coincidentally is on the bus or subway passing through tunnel, connectivity in that case will be affected.

When we receive a `SocketTimeoutException` by[Retrofit](http://square.github.io/retrofit/), what we will do will be to take advantage of the operator [retry](http://reactivex.io/documentation/operators/retry.html#collapseRxJava).

The operator [retry](http://reactivex.io/documentation/operators/retry.html#collapseRxJava) will accept a predicate as in the case of the operator ['filter'](http://reactivex.io/documentation/operators/filter.html), if we return true in that predicate the magic of Rx will issue the 'Observable' again making the Retrofit request for us.

If as most 3 'SocketTimeoutExceptions' are fired, the program flow will go to the `onError` method to manage the error.

```java
@Override
public Observable<List<Comic>> getCharacterComics(int characterId) {

    final String comicsFormat   = "comic";
    final String comicsType     = "comic";

    return mMarvelApi.getCharacterComics(
        characterId, comicsFormat, comicsType)
            .retry((attemps, error) -> 
                error instanceof SocketTimeoutException && 
                attemps < MAX_ATTEMPS);
}
```

## Resources

- [ReactiveX](http://reactivex.io/)

- [Reactive Programming in the Netflix API with RxJava](http://techblog.netflix.com/2013/02/rxjava-netflix-api.html)

- [Use Lambdas on Java 7 and older](https://www.youtube.com/watch?v=DUdhfPh9V_s) - Esko Luontola

- [Grokking RxJava, Part1: The basics](http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/) - Dan Lew




[retrolambda]: https://github.com/orfjackal/retrolambda "RetroLambda"
[reactivex]: http://reactivex.io/

