---
layout: post
permalink: squeezing-gradle-builds
title: Squeezing your Gradle builds
---

Android Studio came from by the hand of **Gradle** as a new tool for the construction and packaging of Android projects. This powerful utility, well exploited, can provide much power and comfort developing complex Android projects. These projects may contain different modules, variants, dependencies, continuous integration systems, code quality, etc.

<br>
![](https://raw.githubusercontent.com/saulmm/Gradle-Stuff/master/art/gradle.png)
<br>


The motivation of this article is nothing more than sharing simple methodologies that I have applied on certain projects.

All the code is uploaded to Github, if someone find any errors, or have any recommendations, will be happy to discuss!.

[https://github.com/saulmm/Gradle-Stuff](https://github.com/saulmm/Gradle-Stuff)

### Working with flavors

Let's imagine that we have three _flavors_ on our project, a **free** _flavor_, **paid**, and a **promo** _flavor_.

```javascript
  productFlavors {

      paid {}

      free {}

      promo {}
  }
```


<br>
To configure them, instead of writing directly to the `build.gradle` at our android module, we will have a new separate file called` variants.gradle` with the different properties of each _flavor_.

_/ variants.gradle_


```
ext {

    basePackageName   = 'saulmm.gradlestuff'
    resAppColorName   = 'build_brand_primary'
    resAppName        = 'build_app_name'
    fieldShowAds      = 'ADS'


    paid =  [

        packageName   : "${basePackageName}.premium",
        appName       : "Gradle Stuff Premium",
        appColor      : "#F44336",
        showAds       : "false",
        versionName   : "2.3.2",
        versionCode   : 4
    ]

    free =  [

        packageName   : "${basePackageName}.free",
        appName       : "Gradle Stuff",
        appColor      : "#4CAF50",
        showAds       : "true",
        versionName   : "4.1.1",
        versionCode   : 15
    ]

    promo = [

        packageName   : "${basePackageName}.promo",
        appName       : "Gradle Stuff Demo",
        appColor      : "#9C27B0",
        showAds       : "true",
        versionName   : "1.0",
        versionCode   : 1
    ]
```

<br>
This file is applied in the `build.gradle` file from the root of the project. Now the submodules can use these properties.

_/ build.gradle_

```
apply from: 'variants.gradle'
```

<br>
Attributes within `ext {}` in the file `variants.gradle` can be accessed in the following way: `rootProject.ext.{field}`


_/ {android module} / build.gradle_

```javascript
productFlavors {

    paid {
        def paid = rootProject.ext.paid

        applicationId paid.packageName
        buildConfigField 'boolean', fieldShowAds, paid.showAds
        resValue 'string', resAppName, paid.appName
        resValue 'color', resAppColorName, paid.appColor
    }

    free {
        def free  = rootProject.ext.free

        applicationId free.packageName
        buildConfigField 'boolean', fieldShowAds, free.showAds
        resValue 'string', resAppName, free.appName
        resValue 'color', resAppColorName, free.appColor
    }

    promo {
        def promo = rootProject.ext.promo

        applicationId promo.packageName
        buildConfigField 'boolean',  fieldShowAds, promo.showAds
        resValue 'string', resAppName, promo.appName
        resValue 'color', resAppColorName, promo.appColor
    }
}
```

<br>

For each  _flavor_, Android allocates certain folders for the use of specific resources or _flavor_ classes. We may replace the values `variants.gradle` to independent files, for me, is more clear to have everything centralized in one place.

![](http://androcode.es/wp-content/uploads/2015/07/Screen-Shot-2015-07-20-at-03.43.52.png)

<br>
At this point, imagine that we have more _flavors_:

_/ presentation / build.gradle_

```javascript
 productFlavors {

    paid {
      def paid = rootProject.ext.paid

      applicationId paid.packageName
      buildConfigField 'boolean', fieldShowAds, paid.showAds
      resValue 'string', resAppName, paid.appName
      resValue 'color', resAppColorName, paid.appColor
    }

    free {
      def free = rootProject.ext.free

      applicationId free.packageName
      buildConfigField 'boolean', fieldShowAds, free.showAds
      resValue 'string', resAppName, free.appName
      resValue 'color', resAppColorName, free.appColor
    }

    promo {
      def promo = rootProject.ext.promo

      applicationId promo.packageName
      buildConfigField 'boolean', fieldShowAds, promo.showAds
      resValue 'string', resAppName, promo.appName
      resValue 'color', resAppColorName, promo.appColor
    }

    christmas {
      def christmas = rootProject.ext.christmas

      applicationId christmas.packageName
      buildConfigField 'boolean', fieldShowAds, christmas.showAds
      resValue 'string', resAppName, christmas.appName
      resValue 'color', resAppColorName, christmas.appColor
    }

    halloween {
      def halloween = rootProject.ext.halloween

      applicationId halloween.packageName
      buildConfigField 'boolean', fieldShowAds, halloween.showAds
      resValue 'string', resAppName, halloween.appName
      resValue 'color', resAppColorName, halloween.appColor
    }

    easter {
      def easter = rootProject.ext.easter

      applicationId easter.packageName
      buildConfigField 'boolean', fieldShowAds, easter.showAds
      resValue 'string', resAppName, easter.appName
      resValue 'color', resAppColorName, easter.appColor
    }

    independence {
      def independence = independence.ext.christmas

      applicationId christmas.packageName
      buildConfigField 'boolean', fieldShowAds, independence.showAds
      resValue 'string', resAppName, independence.appName
      resValue 'color', resAppColorName, independence.appColor
    }
  }
```

<br>
The `build.gradle` file of the android module becomes long and repetitive. If we consult the Android Gradle Plugin [DSL](https://developer.android.com/tools/building/plugin-for-gradle.html), we can see that `productFlavors` delegates  on de class: [NamedDomainObjectContainer](https://docs.gradle.org/current/javadoc/org/gradle/api/NamedDomainObjectContainer.html), this provides a [whenObjectAdded](https://docs.gradle.org/current/javadoc/org/gradle/api/DomainObjectCollection.html#whenObjectAdded (groovy.lang.Closure)) method that allows you to perform an action when an object is added, in this case our 'ProductFlavor'.

_/ presentation / build.gradle_

```javascript
productFlavors.whenObjectAdded { flavor ->

    def flavorData = rootProject.ext[flavor.name]
    flavor.applicationId paid.packageName
    
    flavor.buildConfigField 
        'boolean', fieldShowAds, flavorData.showAds
    flavor.resValue 
        'string', resAppName, flavorData.appName
    flavor.resValue 
        'color', resAppColorName, flavorData.appColor
}
```
<br>
In this way, we can refactor the _flavors_ resulting this `build.gradle` file:


_/ presentation / build.gradle_

```javascript
apply plugin: 'com.android.application'

android {
    compileSdkVersion   androidSdkVersion
    buildToolsVersion   androidToolsVersion

    defaultConfig {

        applicationId     basePackageName
        minSdkVersion     androidMinSdkVersion
        targetSdkVersion  androidSdkVersion
        versionCode       1
        versionName       "1.0"
    }

    buildTypes {

        debug {
            applicationIdSuffix   '.debug'
        }
    }

    productFlavors.whenObjectAdded { flavor ->

        def flavorData = rootProject.ext[flavor.name]
        flavor.applicationId flavorData.packageName
        
        flavor.buildConfigField 
            'boolean', fieldShowAds, flavorData.showAds
        flavor.resValue 
            'string', resAppName, flavorData.appName
        flavor.resValue 
            'color', resAppColorName, flavorData.appColor
    }

    productFlavors {

        paid {}

        free {}

        promo {}

        christmas {}

        halloween {}

        easter {}

        independence {}
    }
}

```



<br>
### Working with modules

Working with independent modules, clarifies the project, especially on a specific architecture where their components are divided into layers.

The modules are declared in the file `settings.gradle`, indicating which will be part of the project

```javascript
include ':presentation', ':domain', ':model'
```

We need indicate which module we are going to use as dependency, example, if we are at the presentation module and we need classes from domain, we need to compile the domain project:


```
dependencies {
    compile project (':domain')
}
```


<br>
##### Dependencies

[Fernando Cejas](http://fernandocejas.com/), gives a brilliant approach regarding how to deal with dependencies, in this example we have a file in the root of the project called `dependencies.gradle` in which stipulate the dependencies to use in each one of the modules. 

By the way, the `.gradle` files can be ordered in any folder, always indicating the required path.

_/ dependencies.gradle_

```javascript
ext {

  butterKnifeVersion = '7.0.1'
  recyclerViewVersion = '21.0.3'
  supportLibrary = '22.2.0'
  firebase = '2.3.1'
  rxAndroidVersion = '0.25.0'
  rxJavaVersion = '1.0.10'

  presentationDependencies = [

      butterKnife      : 
        "com.jakewharton:butterknife:$butterKnifeVersion",
      recyclerView     : 
        "com.android.support:recyclerview-v7:$supportLibrary",
      cardView         : 
        "com.android.support:cardview-v7:$supportLibrary",
      appCompat        : 
        "com.android.support:appcompat-v7:$supportLibrary",
      supportAnnotation: 
        "com.android.support:support-annotations:$supportLibrary",
      rxAndroid        : 
        "io.reactivex:rxandroid:$rxAndroidVersion",
      rxJava           : 
        "io.reactivex:rxjava:$rxJavaVersion",]

  domainDependencies = [

      rxJava: "io.reactivex:rxjava:${rxJavaVersion}",]

  modelDependencies = [

      fireBase: "com.firebase:firebase-client-android:$firebase",
      rxJava  : "io.reactivex:rxjava:$rxJavaVersion",]
}
```

<br>
This file is again applied in the `build.gradle` file from the root of the project.

_/ build.gradle_

```javascript
apply from: 'dependencies.gradle'
```
<br>

_/ presentation / build.gradle_


```javascript
apply plugin: 'com.android.application'

android {
    dependencies {

      def presentationDependencies = 
            rootProject.ext.presentationDependencies

      compile presentationDependencies.butterKnife
      compile presentationDependencies.recyclerView
      compile presentationDependencies.appCompat
      compile presentationDependencies.supportAnnotation
      compile presentationDependencies.rxAndroid
      compile presentationDependencies.rxJava
    }
}
```


### Resources

- [Introduction to Groovy and Gradle](https://www.youtube.com/watch?v=fHhf1xG0pIA) - **Dan Lew**

- [Android 10 coder blog](http://fernandocejas.com/) - **Fernando Cejas**




