# How Web Works
What happens behind the scenes when we type www.google.com in a browser?

## The browser's high level structure

1. **User Interface:** Includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser display except the window where you see the requested page.

2. **Browser Engine:** Marshals actions between the UI and the rendering engine.

3. **Rendering Engine:** Responsible for displaying requested content. For eg. the rendering engine parses HTML and CSS, and displays the parsed content on the screen.

4. **Networking:** For network calls such as HTTP requests, using different implementations for different platforms (behind a platform-independent interface).

5. **UI Backend:** Used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform specific. Underneath it uses operating system user interface methods.

6. **JavaScript Engine:** Interpreter used to parse and execute JavaScript code.

7. **Data Storage:** This is a persistence layer. The broswer may need to save data locally, such as cookies. Browsers also support storage mechanisms such as [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB) and [FileSystem](https://developer.chrome.com/apps/fileSystem).

![Browser Components](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/layers.png)

Note: Browsers such as Chrome run multiple instances of the rendering engine: one for each tab. Each tab runs in a separate process.

*More reading:*

[How Browsers Work: Behind the scenes of modern web browsers](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)