---
layout: post
title: Oxygeroid
comments: true
nav: false
modified: 02-Jul-2017 (23:40)
---

<p>
    <img src="../assets/img/oxp-logo.png" id="oxp-logo">
</p>



last modified: {{ page.modified | downcase }}
{: .last_modified }

## Note

> This library is not longer maintained. Use android's native [data binding](https://developer.android.com/topic/libraries/data-binding/index.html) instead.

## Introduction

Oxygeroid is an annotation processor for Android which parses `XML Layouts` and generates classes containing reference to views defined in `XML`.

## Example

For following `layout file`, `Oxygeroid` will generate a class with name `MainActivityViews` (assuming `@OxyViews` annotation is present on a class named `MainActivity`) which contains references to all views (having IDs) in `layout file`.


{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.mallaudin.testprocessor.MainActivity">

    <TextView
        android:id="@+id/title"
        android:layout_centerInParent="true"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

    <TextView
        android:id="@+id/subject"
        android:layout_alignParentBottom="true"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</RelativeLayout>
{% endhighlight %}

Add `OxyViews` annotation.

{% highlight java %}
@OxyViews("activity_main") // generate view factory
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    } // onCreate

} // MainActivity
{% endhighlight %}

For the above layout and `@OxyViews` annotation, oxygeroid will generate the follwing `view factory` class.

{% highlight java %}
public final class MainActivityViews {

  public final TextView title;
  public final TextView subject;

  private MainActivityViews(final MainActivity root) {
    title = (TextView) root.findViewById(R.id.title);
    subject = (TextView) root.findViewById(R.id.subject);
  }

  public static MainActivityViews newInstance(final MainActivity root) {
    return new MainActivityViews(root);
  }

}
{% endhighlight %}


## Usage

### Step 1

Specify resource package (using `@OxyConfig` annotation) for refering `R` from generated classes. `@OxyConfig` annotation can be added on any class but to make things clear, I would prefer to add it on `Application class`. e.g.

{% highlight java %}
@OxyConfig(resourcePackage = "allaudin.github.io.oxytemp")
public class TestProcessorApp extends Application {
	// todo
} // TestProcessorApp

{% endhighlight %}

### Step 2

Add `@OxyViews` annotation with `xml file name` to generate `view factory` from that `xml`. This annotation can only be added to `activity classes` (if you don't specify the factory type, see [FactoryType](https://allaudin.github.io/oxygeroid/#factorytype)).  e.g.

{% highlight java %}
@OxyViews("activity_main")
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    } // onCreate

} // MainActivity
{% endhighlight %}


## Factory Type

`@OxyViews` without any FactoryType specified, will assume the annotated class, an Activity and will call findViewById method on the instance of that class. It works fine if you annotate an activity with `@OxyViews` but sometimes we need to pass a root view against which we find views by id.

To address this issue, use `@OxyViews` with FactoryType,VIEW factory type. Now, the generated class will accept View as an argument and will find view against this (passed as argument) view. e.g.

{% highlight java %}
@OxyViews(value = "activity_main", type = FactoryType.VIEW)
{% endhighlight %}


## Skipping a View

Any view with `oxyskip` tag will be ignore by `Oxygeroid` i.e. it will not generate view field in generated class.


{% highlight xml %}
<View
        android:id="@+id/my_view"
        android:tag="oxyskip"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

{% endhighlight %}

## Custom Class Name

Class name of generated class can be customized by mentioning `className` in `@OxyViews` annotation. e.g.

{% highlight java %}
@OxyViews(value = "activity_main", className = "MyCustomClassName")
{% endhighlight %}

## Add to Gradle

Add following lines to your **app level** dependency block

{% highlight gradle %}
compile  'io.github.allaudin:oxygeroid-annotations:latest-version'
annotationProcessor  'io.github.allaudin:oxygeroid-processor:latest-version'
{% endhighlight %}

## Download Latest Version

<a href='https://bintray.com/mallaudin/android/oxygeroid-processor/_latestVersion'>Oxygeroid Processor <img src='https://api.bintray.com/packages/mallaudin/android/oxygeroid-processor/images/download.svg' id="download"></a>

<a href='https://bintray.com/mallaudin/android/oxygeroid-annotations/_latestVersion'>Oxygeroid Annotations <img src='https://api.bintray.com/packages/mallaudin/android/oxygeroid-annotations/images/download.svg' id="download"></a>

## License

{% highlight text %}

Copyright 2017 M.Allaudin

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

{% endhighlight %}
