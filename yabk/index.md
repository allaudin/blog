---
layout: doc
title: YABK
comments: true
nav: false
modified: 2-Jul-2017 (23:31)
logo: "/assets/img/yabk_logo.png"
---

last modified: {{ page.modified | downcase }} 
{: .last_modified }


YABK (*Yet Another Boilerplate Killer*) is an annotation processor for generating mutators/accessor and parcelable implementation of java POJOs. 

## Background

For the applications which depend heavily on WEB APIs, we have to model network responses and in Android sometimes we need to make them 
Parcel too. 

A lot of these models, doesn't contain any logic but methods for accessing or setting fields. Generating these fields or parcelables every time we have to update the model is cumbersome. 

> YABK addresses this issue and generates **accessor/mutators** and **parcelable** implementation of models at compile time.

## Getting Started

For processing any POJO with `Yabk`, add `@YabkProcess` annotation on required class.

{% highlight java %}
@YabkProcess
abstract class $CustomerCodeModel {

    String customerId;
    String status;

}
{% endhighlight %}

Yabk generated class for above example

{% highlight java %}
public class CustomerCodeModel extends $CustomerCodeModel implements Parcelable {
  public static final Creator<CustomerCodeModel> CREATOR = new Creator<CustomerCodeModel>() {
          @Override
          public CustomerCodeModel createFromParcel(Parcel in) {
              return new CustomerCodeModel(in);
          }

          @Override
          public CustomerCodeModel[] newArray(int size) {
              return new CustomerCodeModel[size];
          }
      };

  public CustomerCodeModel() {
  }

  protected CustomerCodeModel(Parcel in) {
    this.status = in.readString();
    this.customerId = in.readString();
  }

  public void setStatus(String status) {
    this.status = status;
  }

  public String getStatus() {
    return this.status;
  }

  public void setCustomerId(String customerId) {
    this.customerId = customerId;
  }

  public String getCustomerId() {
    return this.customerId;
  }

  @Override
  public void writeToParcel(Parcel dest, int flags) {
    dest.writeString(status);
    dest.writeString(customerId);
  }

  @Override
  public int describeContents() {
    return 0;
  }
}

{% endhighlight %}

## Class rules

- Class must be **abstract**

- Class must have a non-private **no-arg constructor**

- Field, for which accessor or mutator is generated must be **package private** or **protected**


## Class naming rules

- By convention, for classes with names starting with `$` sign, `Yabk` generates classes with same name by removing `$` sign. e.g.
for class `abstract class $User`, `Yabk` generates `public class User`.

- If class name is not prepended with `$` sign, `Yabk` generates classes with same name appended with `Yabk`. e.g.
for class `abstract class User`, `Yabk` generates `public class YabkUser`.

- If any of the above options do not fill you needs, `Yabk` allows defining custom class names in `@YabkProcess` annotation. e.g.
for a class annotated with `@YabkProcess(className = "CustomUser")`, `Yabk` generates a class with name `CustomUser`.

## Generating required methods only

`Yabk` provides option for generating only accessor or mutator methods. By default it generates both getters and setters. e.g.

For generating only accessors, annotate the class with following annotation

{% highlight java %}
@YabkProcess(methods = Methods.ACCESSORS)
{% endhighlight %}

For mutators only, use following annotation

{% highlight java %}
@YabkProcess(methods = Methods.MUTATORS)
{% endhighlight %}

## Replacing null strings with empty strings

For generating accessor methods which check for null strings and convert them to empty strings, specify `nonNullStrings = true` in `@YabkProcess` annotation. e.g. 

{% highlight java %}
@YabkProcess(nonNullStrings = true)
{% endhighlight %}

## Limitations

- Doesn't work with generic models

- Does not work with POJOs which have wild card types fields

- Does not work with fields having nested types

- Does not work with bounded type fields

## Add to Gradle

Add following lines to your **app level** dependency block

{% highlight gradle %}
compile  'io.github.allaudin:yabk:latest-version'
annotationProcessor  'io.github.allaudin:yabk-compiler:latest-version'
{% endhighlight %}

## Download Lastest Version

<a href='https://bintray.com/mallaudin/android/yabk/_latestVersion'>Yabk<img src='https://api.bintray.com/packages/mallaudin/android/yabk/images/download.svg' id="download"></a>

<a href='https://bintray.com/mallaudin/android/yabk-compiler/_latestVersion'>Yabk Compiler<img src='https://api.bintray.com/packages/mallaudin/android/yabk-compiler/images/download.svg' id="download"></a>

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