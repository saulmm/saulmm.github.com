---
layout: post
---

This is the first in a series of articles on how to setup an environment to develop an android scalable, maintainable and testable project, in this series I will cover some patterns and libraries used in some way to not go crazy on the day day of an android developer.

##Scenario

As an example, I will rely on the following project, is a proof of concept of a simple catalog of films, which can be marked as views or pendings.

The information about the films is taken from a public API called [themoviedb](https://www.themoviedb.org/documentation/api), you can find nice documentation in this section at [Apiary](http: // docs. themoviedb.apiary.io/#).

This project is based on the pattern [Model View Presenter](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter) also implements some _guidelines_ of [Material Design](http://www.google.com/design/spec/material-design/introduction.html) like transitions, structures, animations, colors etc ...

__Github__ - [Https://github.com/saulmm/Material-Movies](https://github.com/saulmm/Material-Movies)

![](https://googledrive.com/host/0B62SZ3WRM2R2Y25FVHdyWHdQU0U)

<br>
##Architecture:
<br>
To design the architecture, I have relied on the pattern [_Model View Presenter_](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter), which is a variation of the architecture pattern [_Model View Controller_](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).

This pattern tries to abstract the **business logic** of the presentation layer, in android this is important, because the own _framework_ facilitates the coupling of these two parts together with the data layer, a clear example is the _Adapters_ or the _CursorLoaders_.

This architecture facilitates the exchange of views without having to modify the business logic layer and the data layer, reuse the domain or vary between various data sources such as a database or a REST API.

<br>
###Overview
<br>
The structure can be divided into three main layers: __presentation__, __model__ and __domain__.
<br>
![](https://5453d9b6c8c379e029e37a32861f12f1e6c1c716.googledrive.com/host/0B62SZ3WRM2R2eGczcWh3MERkRGc)

<br>
####Presentation
<br>
The __presentation__ layer is the responsible as its name indicates to display the graphical interface and providing it with data.
<br>
####Model
<br>
The __model__, will be responsible for providing information, this layer does not know about the domain, not the presentation, could implement a connection and a interface with a database, with a REST API, or any other ways of persistence.

In this layer there are also implemented the entities of the application, the class that represents a movie, category, etc...

<br>
###Domain
<br>
The __domain__ layer is completely independent of the presentation layer, in it will reside business logic of your application.

<br>
###Implementation
<br>
The domain and the model layer are distributed in two java modules, the presentation layer is represented by the app module, which is the Android application, there is one more module which is a common module that shares libraries and utilities.

![](https://googledrive.com/host/0B62SZ3WRM2R2elJfT0JiZnlTcE0)

<br>
###Domain module
<br>
The domain module hosts usecases and their implementations, it is the **business logic** of the application.

This module is totally independent of the Android _framework_.

Its dependencies are the model module and common module.

An usecase could be to obtain the total valuation of various categories of films, to see which category is the most requested, the usecase would need to obtain the information and then make a calculation with it, that information is requested from the model.

```
dependencies {
    compile project (':common')
    compile project (':model')
}
```
<br>
###Model module
<br>
The module model is responsible for managing the information, select, save, delete etc ... In a first version only manage operations with the information films API.

It also implements entities, such as `TvMovie`, to represent a movie.

Its dependencies only be the common module and for now, the library used to manage requests to the API, in this case [Retrofit](http://square.github.io/retrofit/) by [Square](http : //square.github.io/):

```
dependencies {
    compile project(':common')
    compile 'com.squareup.retrofit:retrofit:1.9.0'
}
```
<br>
###Presentation module
<br>
Is the Android application itself, with its resources, _assets_, logic etc...

It also interact with the domain by running the usecases, an example would be to obtain the list the current movies of a certain time, or request specific data from a movie.

In this module there are **presenters** and **views**.

Each `Activity`, `Fragment`, `Dialog`, implement a `MVPView` interface, it shall specify operations to show, hide and paint information which has to support the view.

For example, the view, `PopularMoviesView`, specify the operations to display the list of current movies is be implemented by sight,` MoviesActivity`.

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
<br>
For this project I have chosen a **Message Bus** system, this system is very useful for _Broadcast_  events, or to communicate between components, in this case it fits perfectly.

Basically events are sent through a Bus, and the classes interested to consume that event subscribe to that Bus.

Using this system the modules have a low coupling.

To implement the system bus, I used the library [Otto](http://square.github.io/otto/) of [Square](http://square.github.io/).

For this I declared two buses, in this case, one for the communicate the usecases with the REST API, and the other to send events to the presentation layer.

`REST_BUS` use any thread to handle events, while the` UI_BUS` send events in the thread's default library, the thread of the user interface.

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
```
dependencies {
    compile 'com.squareup:otto:1.3.5'
}
```

Finally, imagine the following example, a user opens the application and the most popular films are shown.

The presenter, when the view `onCreate` method is call, is registered at bus GUI to receive events. The presenter eliminates the register when the `onStop()` method is called. Then, run the `GetMoviesUseCase` use case.

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

To receive the event, the presenter must implement a method that receives a parameter with the same data type as the event sent by the bus, it must also contain a notation called `@Subscribe`.

```java
    @Subscribe
    @Override
    public void onPopularMoviesReceived(PopularMoviesApiResponse popularMovies) {

        popularMoviesView.hideLoading();
        popularMoviesView.showMovies(popularMovies.getResults());
    }
```
<br>
**Resources:**
<br>
[Architecting Android…The clean way?](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/) - Fernando Cejas

[Effective Android UI](https://github.com/pedrovgs/EffectiveAndroidUI) - Pedro Vicente Gómez Sanchez

[Reactive programming and message buses for mobile](http://csaba.palfi.me/reactive-and-buses-for-mobile/)  - Csaba Palfi

[The clean architecture](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)  - Uncle Bob

