Goose for Android
=================
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-goose-green.svg?style=true)](https://android-arsenal.com/details/1/4163)

What's this?
------------
Goose is an article extractor for web pages. This means that the algorithm is capable of determining where to look for relevant article information from a website, 
properly extracts "interesting" data, picks out the best images from the page and determines a confidence factor for the top-picked image based on various meta 
information from the given page.  
The command line Goose library (written in Scala) is licensed by Gravity.com under Apache 2.0 license, see the LICENSE file for more details.

About Android specific stuff
----------------------------
Android implementation addresses the issues Scala imposes on Android - such as using external programs (image-magick), using outdated HTTP libraries (Apache),
downloading images to any location on the disk (no open disk on Android), managing cache, SD cards, battery consumption, network issues, redirects, etc.
Since version `1.5.0`, Goose for Android also uses the official `HttpURLConnection` that makes it compliant with Google's requests about deprecating outdated
SSL libraries such as OpenSSL.  
A sample of how to use Goose for Android can be found in `DemoActivity.java` in the `app` folder source.

Requirements
------------
- Android 4.0 or later (Minimum SDK level 14)
- Android Studio (to compile and use)
- **Eclipse is not supported**

Getting Started
---------------
1. [Download Android Studio](http://developer.android.com/sdk/index.html)
2. Launch Android Studio
3. Start your new project
4. Open your project's main Gradle file, in root directory (**/build.gradle**)
5. Make sure you are using **jcenter()** in the repository block (**mavenCentral()** should work too)
6. Open your app module's Gradle file, for example **/app/build.gradle**
7. In **dependencies** block, add the following line: `compile 'me.angrybyte.goose:goose:1.5.0'`
8. Click **Tools/Android/Sync Project with Gradle Files** or click on the *Sync* icon in the top toolbar
9. Click **Run/Run 'app'** to see if it's resolved correctly

This will run the app on your device. You may need to download a newer version of Gradle, which will be available in the Android Studio UI if compile fails.

Sample usage
------------
Make sure you do this on a background thread (or in an `AsyncTask`). Some of the results are in the `screenshots` directory in the project root.
```java
Configuration config = new Configuration(getCacheDir().getAbsolutePath());
ContentExtractor extractor = new ContentExtractor(config);

Article article = extractor.extractContent(url);
if (article == null) {
    Log.e(TAG, "Couldn't load the article, is your URL correct, is your Internet working?");
    return;
}

String details = article.getCleanedArticleText();
if (details == null) {
    Log.w(TAG, "Couldn't load the article text, the page is messy. Trying with page description...");
    details = article.getMetaDescription();
}

Bitmap photo = null;
if (article.getTopImage() != null) { // topImage value holds an absolute URL
    photo = GooseDownloader.getPhoto(article.getTopImage().getImageSrc());
}
```

Data you might be interested in is always available in the `Article` object:
- **Title**: Page title, defined in the `<title>` tag
- **Meta Description**: Page description, defined in the meta description tag of the `<head>` parent
- **Meta Keywords**: Page keywords, defined in the meta keywords tag of the `<head>` parent
- **Top Image**: A best-guess photo for the article. This may be an image from OpenGraph, meta, promo, or just the visually closest image to the relevant text
- **Cleaned Article Text**: Parsed and prepared, HTML-cleaned article text from the given webpage. Usually the longest text contained in an `<article>` tag, but Goose checks for good `<div>` and `<p>` tags as well
- **Publish Date**: A parsed date of publishing for the relevant article. You need a special date extraction in order for this to work. Check source for more information

Support
-------
If you found an error while using the library, please [file an issue](https://github.com/milosmns/goose/issues/new).
All patches are encouraged, and may be submitted by [forking this project](https://github.com/milosmns/goose/fork) and
submitting a pull request through GitHub.
Some more help can be found here:
- StackOverflow [here](http://stackoverflow.com/questions/tagged/goose) or [here](http://stackoverflow.com/questions/tagged/goose-android)
- On [my blog](http://angrybyte.me)

Help wanted
-----------
This is still not very clean and nice. It works, but it is not clean. Some of the things to address:

- Comment code and fix javadoc issues. Some comments are... useless, obsolete or just boring
- Remove code that produces warnings; the base repo had a lot of them and almost none were fixed
- Generally clean up the codebase. Remove obsolete references and make the API readable
- Add a task to export the library to `jar` archive

_Tip_: to comment all logs use:  
```find . -name "*\.java" | xargs grep -l 'Log\.' | xargs sed -i 's/Log\./;\/\/ Log\./g'```  
_Tip_: to uncomment all logs use:  
```find . -name "*\.java" | xargs grep -l 'Log\.' | xargs sed -i 's/;\/\/ Log\./Log\./g'```

Any help is appreciated.
