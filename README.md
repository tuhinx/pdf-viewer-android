# 📚 Android PDF Viewer


A powerful and feature-rich Android library for displaying PDF documents with smooth animations, gestures, zoom, and double tap support. Built on top of [PdfiumAndroid](https://github.com/barteksc/PdfiumAndroid) for PDF decoding. Compatible with API 15 (Android 4.0) and higher.

[![](https://jitpack.io/v/Tuhinx/pdf-viewer-android.svg)](https://jitpack.io/#Tuhinx/pdf-viewer-android)

## 🚀 Features

- 📱 Smooth animations and gestures
- 🔍 Zoom and double tap support
- 📄 Multiple page display options
- 🎯 Customizable scroll handles
- 🔗 Link handling support
- 🌙 Night mode support
- 📏 Multiple page fit policies
- 🎨 High-quality bitmap rendering

## 📦 Installation

### Gradle

```groovy
dependencyResolutionManagement {
		repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
		repositories {
			mavenCentral()
			maven { url 'https://jitpack.io' }
		}
	}

dependencies {
    implementation 'com.github.Tuhinx:pdf-viewer-android:3.0.0'
}

```

## For Kotlin

```gradle.kts
	dependencyResolutionManagement {
		repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
		repositories {
			mavenCentral()
			maven { url = uri("https://jitpack.io") }
		}
	}


dependencies {
    implementation("com.github.Tuhinx:pdf-viewer-android:3.0.0")
}

```

Library is available in jcenter repository, probably it'll be in Maven Central soon.

## 🔒 ProGuard Configuration

If you are using ProGuard, add following rule to proguard config file:

```proguard
-keep class com.shockwave.**
```

## 🛠️ Usage

### 1. Add PDFView to your layout

```xml
<com.github.pdfviewer.PDFView
        android:id="@+id/pdfView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```

### 2. Load a PDF file

All available options with default values:

```java
pdfView.fromUri(Uri)
or
pdfView.fromFile(File)
or
pdfView.fromBytes(byte[])
or
pdfView.fromStream(InputStream) // stream is written to bytearray - native code cannot use Java Streams
or
pdfView.fromSource(DocumentSource)
or
pdfView.fromAsset(String)
    .pages(0, 2, 1, 3, 3, 3) // all pages are displayed by default
    .enableSwipe(true) // allows to block changing pages using swipe
    .swipeHorizontal(false)
    .enableDoubletap(true)
    .defaultPage(0)
    // allows to draw something on the current page, usually visible in the middle of the screen
    .onDraw(onDrawListener)
    // allows to draw something on all pages, separately for every page. Called only for visible pages
    .onDrawAll(onDrawListener)
    .onLoad(onLoadCompleteListener) // called after document is loaded and starts to be rendered
    .onPageChange(onPageChangeListener)
    .onPageScroll(onPageScrollListener)
    .onError(onErrorListener)
    .onPageError(onPageErrorListener)
    .onRender(onRenderListener) // called after document is rendered for the first time
    // called on single tap, return true if handled, false to toggle scroll handle visibility
    .onTap(onTapListener)
    .onLongPress(onLongPressListener)
    .enableAnnotationRendering(false) // render annotations (such as comments, colors or forms)
    .password(null)
    .scrollHandle(null)
    .enableAntialiasing(true) // improve rendering a little bit on low-res screens
    // spacing between pages in dp. To define spacing color, set view background
    .spacing(0)
    .autoSpacing(false) // add dynamic spacing to fit each page on its own on the screen
    .linkHandler(DefaultLinkHandler)
    .pageFitPolicy(FitPolicy.WIDTH) // mode to fit pages in the view
    .fitEachPage(false) // fit each page to the view, else smaller pages are scaled relative to largest page.
    .pageSnap(false) // snap pages to screen boundaries
    .pageFling(false) // make a fling change only a single page like ViewPager
    .nightMode(false) // toggle night mode
    .load();
```

- `pages` is optional, it allows you to filter and order the pages of the PDF as you need

## 📜 Scroll Handle

Scroll handle is a replacement for **ScrollBar** from 1.x branch.

From version 2.1.0, putting **PDFView** in **RelativeLayout** to use **ScrollHandle** is not required - you can use any layout.

To use scroll handle, register it using method `Configurator#scrollHandle()`:

```java
.scrollHandle(new DefaultScrollHandle(this))
```

**DefaultScrollHandle** is placed on the right (when scrolling vertically) or on the bottom (when scrolling horizontally).
Use `new DefaultScrollHandle(this, true)` to place handle on the left or top.

You can also create custom scroll handles, just implement **ScrollHandle** interface.
All methods are documented as Javadoc comments on interface [source](https://github.com/barteksc/AndroidPdfViewer/tree/master/android-pdf-viewer/src/main/java/com/github/barteksc/pdfviewer/scroll/ScrollHandle.java).

Predefined providers can be used with shorthand methods:

```
pdfView.fromUri(Uri)
pdfView.fromFile(File)
pdfView.fromBytes(byte[])
pdfView.fromStream(InputStream)
pdfView.fromAsset(String)
```

Custom providers may be used with `pdfView.fromSource(DocumentSource)` method.

## 🔗 Link Handling

Version 4.0.0 introduced support for links in PDF documents. By default, **DefaultLinkHandler** is used:

- Clicking on a link that references a page in the same document jumps to the destination page
- Clicking on a link that targets a URI opens it in the default application

## 📐 Page Fit Policies

Library supports three page fitting modes:

- `WIDTH` - width of widest page is equal to screen width
- `HEIGHT` - height of highest page is equal to screen height
- `BOTH` - based on widest and highest pages, every page is scaled to be fully visible on screen

Apart from selected policy, every page is scaled to have size relative to other pages.

Fit policy can be set using `Configurator#pageFitPolicy(FitPolicy)`. Default policy is **WIDTH**.

## 💡 Tips & Tricks

### Bitmap Quality

By default, bitmaps are compressed with `RGB_565` format. Force `ARGB_8888` rendering with:

```java
pdfView.useBestQuality(true)
```

### Double Tap Zooming

Three zoom levels:

- Min (default 1)
- Mid (default 1.75)
- Max (default 3)

Change zoom levels using:

```java
void setMinZoom(float zoom);
void setMidZoom(float zoom);
void setMaxZoom(float zoom);
```

### ViewPager-like Scrolling

Use these settings for ViewPager-like behavior:

```java
.swipeHorizontal(true)
.pageSnap(true)
.autoSpacing(true)
.pageFling(true)
```

### Why I cannot open PDF from URL?

Downloading files is long running process which must be aware of Activity lifecycle, must support some configuration,
data cleanup and caching, so creating such module will probably end up as new library.

### How can I show last opened page after configuration change?

You have to store current page number and then set it with `pdfView.defaultPage(page)`, refer to sample app

### How can I fit document to screen width (eg. on orientation change)?

Use `FitPolicy.WIDTH` policy or add following snippet when you want to fit desired page in document with different page sizes:

```java
Configurator.onRender(new OnRenderListener() {
    @Override
    public void onInitiallyRendered(int pages, float pageWidth, float pageHeight) {
        pdfView.fitToWidth(pageIndex);
    }
});
```

## 📝 License

```
Copyright 2024 by Tuhinx

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
