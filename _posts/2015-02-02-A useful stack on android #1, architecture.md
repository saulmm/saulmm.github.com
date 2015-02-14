---
layout: post
---
<br>
This is the first in a series of articles on how to setup an environment to develop an android scalable, maintainable and testable project, in this series I will cover some patterns and libraries used in some way to not go crazy on the day day of an android developer.

<br>
##Scenario


As an example, I will rely on the following project, is a proof of concept of a simple catalog of films, which can be marked as views or pendings.

The information about the films is taken from a public API called [themoviedb](https://www.themoviedb.org/documentation/api), you can find nice documentation in this section at [Apiary](http://docs.themoviedb.apiary.io/).

The project is based on the pattern [_Model View Presenter_](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter), also implements some _guidelines_ of [_Material Design_](http://www.google.com/design/spec/material-design/introduction.html) like transitions, structures, animations, colors etc...

All the code is available on [__Github__](https://github.com/saulmm/Material-Movies), so feel free to take a look, also there is a [__video__](https://plus.google.com/+SaulMolineroMalvido/posts/9MysXxm5s9U?pid=6111260593672241538&oid=110695998480380973857) showing the app behavior.
<br>
<br>
![](http://res.cloudinary.com/dttcwxrjo/image/upload/v1422988952/1_ntqfre.png)

<br>
##Architecture:
To design the architecture, I have relied on the pattern: [_Model View Presenter_](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter), which is a variation of the architecture pattern [_Model View Controller_](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).

This pattern tries to abstract the **business logic** of the presentation layer, in android this is important, because the own _framework_ facilitates the coupling of these two parts together with the data layer, a clear example is the _Adapters_ or the _CursorLoaders_.

This architecture facilitates the exchange of views without having to modify the business logic layer and the data layer. Is esasy to reuse the domain or vary between various data sources such as a database or a REST API.

<br>
###Overview
The structure can be divided into three main layers: 

- __presentation__
- __model__
- __domain__

![](http://res.cloudinary.com/dttcwxrjo/image/upload/v1422988949/0B62SZ3WRM2R2eGczcWh3MERkRGc-e1422883852292_hfs4r6.png)

<br>
####Presentation
The __presentation__ layer is the responsible to display the graphical interface and providing it with data.

####Model
The __model__, will be responsible for providing information, this layer does not know about the domain, not the presentation, could implement a connection and a interface with a database, with a REST API, or any other ways of persistence.

In this layer there are also implemented the entities of the application, the class that represents a movie, category, etc...


####Domain
The __domain__ layer is completely independent of the presentation layer, in it will reside business logic of your application.

<br>
###Implementation
The domain and the model layer are distributed in two java modules, the presentation layer is represented by the app module, which is the Android application, there is one more module which is a common module that shares libraries and utilities.

<img src="http://res.cloudinary.com/dttcwxrjo/image/upload/v1422988944/0B62SZ3WRM2R2elJfT0JiZnlTcE01_ospwxi.png" width="520" height="600">

<br>
####Domain module
The domain module hosts usecases and their implementations, it is the **business logic** of the application.

This module is totally independent of the Android _framework_.

Its dependencies are the model module and common module.

An usecase could be to obtain the total rating of various categories of films, to see which category is the most requested, the usecase would need to obtain the information and then make a calculation with it, that information is provided by the model.

```javascript
dependencies {
    compile project (':common')
    compile project (':model')
}
```
####Model module
The model module is responsible for managing the information, select, save, delete etc... In a first version I only manage operations with the information films API.

It also implements entities, such as `TvMovie`, to represent a movie.

Its dependencies only are the common module and for now, the library used to manage requests to the API, in this case I have used [Retrofit](http://square.github.io/retrofit/) by [Square](http : //square.github.io/), I will talk about retrofit in following posts.

```javascript
dependencies {
    compile project(':common')
    compile 'com.squareup.retrofit:retrofit:1.9.0'
}
```
####Presentation module
Is the Android application itself, with its resources, _assets_, logic etc...

It also interact with the domain by running the usecases, an example would be to obtain the list the current movies of a certain time, or request specific data from a movie.

In this module there are **presenters** and **views**.

Each `Activity`, `Fragment`, `Dialog`, implement a `MVPView` interface, it specifies the operations to show, hide and paint information which has to support the view.

For example, the view, `PopularMoviesView`,specifies the operations to display the list of current movies is be implemented by the`MoviesActivity`.

```java
public interface PopularMoviesView extends MVPView {

    void showMovies (List<TvMovie> movieList);

    void showLoading ();

    void hideLoading ();

    void showError (String error);

    void hideError ();
}
```

The pattern: _MVP_ says that the views have to be **as simple as possible**, is the presenter who decides their behavior.

```java
public class MoviesActivity extends ActionBarActivity implements
    PopularMoviesView, ... {

    ...
    private PopularShowsPresenter popularShowsPresenter;
    private RecyclerView popularMoviesRecycler;
    private ProgressBar loadingProgressBar;
    private MoviesAdapter moviesAdapter;
    private TextView errorTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        ...
        popularShowsPresenter = new PopularShowsPresenterImpl(this);
        popularShowsPresenter.onCreate();
    }

    @Override
    protected void onStop() {

        super.onStop();
        popularShowsPresenter.onStop();
    }

    @Override
    public Context getContext() {

        return this;
    }

    @Override
    public void showMovies(List<TvMovie> movieList) {

        moviesAdapter = new MoviesAdapter(movieList);
        popularMoviesRecycler.setAdapter(moviesAdapter);
    }

    @Override
    public void showLoading() {
        
        loadingProgressBar.setVisibility(View.VISIBLE);
    }

    @Override
    public void hideLoading() {

        loadingProgressBar.setVisibility(View.GONE);
    }

    @Override
    public void showError(String error) {
        
        errorTextView.setVisibility(View.VISIBLE);
        errorTextView.setText(error);
    }

    @Override
    public void hideError() {

        errorTextView.setVisibility(View.GONE);
    }

    ...
}
```

The usecases will be executed from the presenters, they will receive the response and manage the behavior of the views.

```java
public class PopularShowsPresenterImpl implements PopularShowsPresenter {

    private final PopularMoviesView popularMoviesView;

    public PopularShowsPresenterImpl(PopularMoviesView popularMoviesView) {

        this.popularMoviesView = popularMoviesView;
    }

    @Override
    public void onCreate() {

        ...
        popularMoviesView.showLoading();

        Usecase getPopularShows = new GetMoviesUsecaseController(GetMoviesUsecase.TV_MOVIES);
        getPopularShows.execute();
    }

    @Override
    public void onStop() {

        ...
    }


    @Override
    public void onPopularMoviesReceived(PopularMoviesApiResponse popularMovies) {

        popularMoviesView.hideLoading();
        popularMoviesView.showMovies(popularMovies.getResults());
    }
}

```
<br>
##Communication
For this project I have chosen a **Message Bus** system, this system is very useful for _Broadcast_  events, or to establish a communication between components, the last case fits perfectly.

Basically events are sent through a **Bus**, and the classes interested to consume that event have to **subscribe** to that Bus.

Using this system the modules have a very low coupling.

To implement the system bus, I used the library [**Otto**](http://square.github.io/otto/) by [Square](http://square.github.io/).

I declared two buses, to communicate the usecases with the REST API, and the other to send events to the presentation layer.

`REST_BUS` will use any thread to handle events, while the `UI_BUS` sends events over the default thread, this thread is the main thread.

```java
public class BusProvider {

    private static final Bus REST_BUS = new Bus(ThreadEnforcer.ANY);
    private static final Bus UI_BUS = new Bus();

    private BusProvider() {};

    public static Bus getRestBusInstance() {

        return REST_BUS;
    }

    public static Bus getUIBusInstance () {

        return UI_BUS;
    }
}
```

This class is managed by the common module, because all modules have access to it to interact with the buses.

```javascript
dependencies {
    compile 'com.squareup:otto:1.3.5'
}
```

Finally, think about the following example, the user opens the application and the most popular films are shown.

When the `onCreate` method in the Android `View` is call, the presenter is **subscribed** at the UI_BUS to receive events. The presenter **unsubscribes** itself when the `onStop()` method is called, then, the presenter runs the `GetMoviesUseCase` usecase.

```java
    @Override
    public void onCreate() {
        
        BusProvider.getUIBusInstance().register(this);

        Usecase getPopularShows = new GetMoviesUsecaseController(GetMoviesUsecase.TV_MOVIES);
        getPopularShows.execute();
    }

    ...

    @Override
    public void onStop() {
       
        BusProvider.getUIBusInstance().unregister(this);
    }
}
```

To receive the event, the presenter must implement a method that receives a parameter with the same datatype as the event sent by the bus, it must to use the annotation: `@Subscribe`.

```java
    @Subscribe
    @Override
    public void onPopularMoviesReceived(PopularMoviesApiResponse popularMovies) {

        popularMoviesView.hideLoading();
        popularMoviesView.showMovies(popularMovies.getResults());
    }
```
<br>

##Resources:
<br>

- [Architecting Android…The clean way?](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/) - Fernando Cejas

- [Effective Android UI](https://github.com/pedrovgs/EffectiveAndroidUI) - Pedro Vicente Gómez Sanchez

- [Reactive programming and message buses for mobile](http://csaba.palfi.me/reactive-and-buses-for-mobile/)  - Csaba Palfi

- [The clean architecture](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)  - Uncle Bob

- [MVP Android](http://antonioleiva.com/mvp-android/) - Antonio Leiva

