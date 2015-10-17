# How Web Works
What happens behind the scenes when we type www.google.com in a browser?

## The browser's high level structure

1. **User Interface:** Includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser display except the window where you see the requested page.

2. **Browser Engine:** [Marshals](http://stackoverflow.com/a/5600887/1672655) actions between the UI and the rendering engine.

3. **Rendering Engine:** Responsible for displaying requested content. For eg. the rendering engine parses HTML and CSS, and displays the parsed content on the screen.

4. **Networking:** For network calls such as HTTP requests, using different implementations for different platforms (behind a platform-independent interface).

5. **UI Backend:** Used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform specific. Underneath it uses operating system user interface methods.

6. **JavaScript Engine:** Interpreter used to parse and execute JavaScript code.

7. **Data Storage:** This is a persistence layer. The broswer may need to save data locally, such as cookies. Browsers also support storage mechanisms such as [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB) and [FileSystem](https://developer.chrome.com/apps/fileSystem).

![Browser Components](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/layers.png)

Note: Browsers such as Chrome run multiple instances of the rendering engine: one for each tab. Each tab runs in a separate process.

## Rendering Engine

A rendering engine is a software component that takes marked up content (such as HTML, XML, image files, etc.) and formatting information (such as CSS, XSL, etc.) and displays the formatted content on the screen.

|Browser           |Engine                       |
|----------------- |:---------------------------:|
|Chrome            | Blink (a fork of WebKit)    |
|Firefox           | Gecko                       |
|Safari            | Webkit                      |
|Opera             | Blink (Presto if < v15) |
|Internet Explorer | Trident                     |
|Edge              | EdgeHTML                    |  

WebKit is an open source rendering engine which started as an engine for the Linux platform and was modified by Apple to support Mac and Windows.

## The Main flow

The rendering engine will start getting the contents of the requested document from the networking layer. This is usually done in 8KB chunks.

After that the basic flow of the rendering engine is:

![Rendering engine basic flow](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/flow.png)

The rendering engine will start parsing the HTML document and convert elements to [DOM](http://domenlightenment.com/) nodes in a tree called the **"content tree"**. 

The engine will parse the style data, both in external CSS files and in style elements. Styling information together with visual instructions in the HTML will be used to create another tree: the **render tree**.
The render tree contains rectangles with visual attributes like color and dimensions. The rectangles are in the right order to be displayed on the screen.

After the construction of the render tree it goes through a **"layout"** process. This means giving each node the exact coordinates where it should appear on the screen.

The next stage is **painting**-the render tree will be traversed and each node will be painted using the UI backend layer.

It's important to understand that this is a gradual process. For better user experience, the rendering engine will try to display contents on the screen as soon as possible. It will not wait until all HTML is parsed before starting to build and layout the render tree. Parts of the content will be parsed and displayed, while the process continues with the rest of the contents that keeps coming from the network.

Given below is Webkit's flow:

![Webit main flow](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/webkitflow.png)









*More reading:*

[How Browsers Work: Behind the scenes of modern web browsers](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)
