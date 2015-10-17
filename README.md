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

![Webkit main flow](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/webkitflow.png)

## Parsing Basics

Parsing: Translating the document to a structure the code can use. The result of parsing is usually a tree of nodes that represent the structure of the document.
 
Grammar: Parsing is based on the syntax rules the document obeys: the language or format it was written in. Every format you can parse must have deterministic grammar consisting of vocabulary and syntax rules. It is called a **context free grammar**.  

Parsing can be separated into two sub processes: lexical analysis and syntax analysis.
              
Lexical analysis: The process of breaking the input into tokens. Tokens are the language vocabulary: the collection of valid building blocks.

Syntax analysis: The applying of the language syntax rules.

Parsers usually divide the work between two components: the lexer (sometimes called tokenizer) that is responsible for breaking the input into valid tokens, and the parser that is responsible for constructing the parse tree by analyzing the document structure according to the language syntax rules. The lexer knows how to strip irrelevant characters like white spaces and line breaks.

![Source document to parse tree](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image011.png)

The parsing process is iterative. The parser will usually ask the lexer for a new token and try to match the token with one of the syntax rules. If a rule is matched, a node corresponding to the token will be added to the parse tree and the parser will ask for another token.

If no rule matches, the parser will store the token internally, and keep asking for tokens until a rule matching all the internally stored tokens is found. If no rule is found then the parser will raise an exception. This means the document was not valid and contained syntax errors.

The job of the HTML parser is to parse the HTML markup into a parse tree. HTML definition is in a DTD (Document Type Definition) format. This format is used to define languages of the SGML family. The format contains definitions for all allowed elements, their attributes and hierarchy. As we saw earlier, the HTML DTD doesn't form a context free grammar.

HTML parsing algorithm consists of two stages: tokenization and tree construction.

Tokenization is the lexical analysis, parsing the input into tokens. Among HTML tokens are start tags, end tags, attribute names and attribute values. The tokenizer recognizes the token, gives it to the tree constructor, and consumes the next character for recognizing the next token, and so on until the end of the input.

![HTML parsing flow](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image017.png)

## DOM Tree

The output tree (the "parse tree") is a tree of DOM element and attribute nodes. DOM is short for Document Object Model. It is the object presentation of the HTML document and the interface of HTML elements to the outside world like JavaScript. The root of the tree is the "Document" object.

The DOM has an almost one-to-one relation to the markup. For example:

```html
<html>
  <body>
    <p>
      Hello World
    </p>
    <div> <img src="example.png"/></div>
  </body>
</html> This markup would be translated to the following DOM tree:
```

This markup would be translated to the following DOM tree:

![DOM Tree](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image015.png)

## Render Tree

While the DOM tree is being constructed, the browser constructs another tree, the render tree. This tree is of visual elements in the order in which they will be displayed. It is the visual representation of the document. The purpose of this tree is to enable painting the contents in their correct order.

A renderer knows how to lay out and paint itself and its children. Each renderer represents a rectangular area usually corresponding to a node's CSS box.

## Render tree's relation to the DOM tree

The renderers correspond to DOM elements, but the relation is not one to one. Non-visual DOM elements will not be inserted in the render tree. An example is the "head" element. Also elements whose display value was assigned to "none" will not appear in the tree (whereas elements with "hidden" visibility will appear in the tree).

There are DOM elements which correspond to several visual objects. These are usually elements with complex structure that cannot be described by a single rectangle. For example, the "select" element has three renderers: one for the display area, one for the drop down list box and one for the button. Also when text is broken into multiple lines because the width is not sufficient for one line, the new lines will be added as extra renderers.
 
Some render objects correspond to a DOM node but not in the same place in the tree. Floats and absolutely positioned elements are out of flow, placed in a different part of the tree, and mapped to the real frame. A placeholder frame is where they should have been.
 
![The render tree and the corresponding DOM tree](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image025.png)
 
In WebKit the process of resolving the style and creating a renderer is called "attachment". Every DOM node has an "attach" method. Attachment is synchronous, node insertion to the DOM tree calls the new node "attach" method.

Building the render tree requires calculating the visual properties of each render object. This is done by calculating the style properties of each element. The style includes style sheets of various origins, inline style elements and visual properties in the HTML (like the "bgcolor" property).The later is translated to matching CSS style properties.

## CSS Parsing

CSS Selectors are matched by browser engines from right to left. Keep in mind that when a browser is doing selector matching it has one element (the one it's trying to determine style for) and all your rules and their selectors and it needs to find which rules match the element. This is different from the usual jQuery thing, say, where you only have one selector and you need to find all the elements that match that selector.

A selector's specificity is calculated as follows:

* Count 1 if the declaration it is from is a 'style' attribute rather than a rule with a selector, 0 otherwise (= a)
* Count the number of ID selectors in the selector (= b)
* Count the number of class selectors, attributes selectors, and pseudo-classes in the selector (= c)
* Count the number of element names and pseudo-elements in the selector (= d)
* Ignore the universal selector

Concatenating the three numbers a-b-c-d (in a number system with a large base) gives the specificity. The number base you need to use is defined by the highest count you have in one of a, b, c and d.
 
Examples:

- *               /* a=0 b=0 c=0 -> specificity =   0 */
- LI              /* a=0 b=0 c=1 -> specificity =   1 */
- UL LI           /* a=0 b=0 c=2 -> specificity =   2 */
- UL OL+LI        /* a=0 b=0 c=3 -> specificity =   3 */
- H1 + *[REL=up]  /* a=0 b=1 c=1 -> specificity =  11 */
- UL OL LI.red    /* a=0 b=1 c=3 -> specificity =  13 */
- LI.red.level    /* a=0 b=2 c=1 -> specificity =  21 */
- #x34y           /* a=1 b=0 c=0 -> specificity = 100 */
- #s12:not(FOO)   /* a=1 b=0 c=1 -> specificity = 101 */
 
WebKit uses a flag that marks if all top level style sheets (including @imports) have been loaded. If the style is not fully loaded when attaching, place holders are used and it is marked in the document, and they will be recalculated once the style sheets were loaded. 




 
 

*More reading:*

[How Browsers Work: Behind the scenes of modern web browsers](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)
