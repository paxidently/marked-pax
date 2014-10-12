# marked-pax

marked-pax is augmented version of [marked](https://github.com/chjj/marked).

## Changes

### GFM-style headers

Original behaviour:

````bash
$ echo '#foo' | marked
<h1 id="foo">foo</h1>

````

GFM-style:

````bash
$ echo '
#Header
# Header
#  Header
' | marked-pax
<p>#Header</p>
<h1 id="header">Header</h1>
<h1 id="header">Header</h1>

````

### &tl; and &gt; escapes

Source: '\<\>', result: '&lt;&gt;'.

## New syntax

### Headers with anchors

Source:

````markdown
# ^chapter-1 Случайный Юпитер — актуальная национальная задача

^chapter2 Астероидный возмущающий фактор в XXI веке
===================================================
````

````html
<h1 id="chapter-1">Случайный Юпитер — актуальная национальная задача</h1>
<h1 id="chapter2">Астероидный возмущающий фактор в XXI веке</h1>
````

Renderer method: `heading (text, level, raw, anchor)`

### [KaTeX](http://khan.github.io/KaTeX) formulas

Source:

````markdown
$
\KaTeX
$

$\text{[links](not works)}$
````

Result:

````html
<p><span class="katex"><span class="katex-inner"><span class="strut" style="height:0.68333em;"></span><span class="strut bottom" style="height:1.0302031249999999em;vertical-align:-0.34687312499999995em;"></span><span class="base textstyle uncramped"><span class="katex-logo"><span class="k">K</span><span class="a">A</span><span class="t">T</span><span class="e">E</span><span class="x">X</span></span></span></span></span></p>
<p><span class="katex"><span class="katex-inner"><span class="strut" style="height:0.75em;"></span><span class="strut bottom" style="height:1em;vertical-align:-0.25em;"></span><span class="base textstyle uncramped"><span class="text mord textstyle uncramped"><span class="mord">[</span><span class="mord">l</span><span class="mord">i</span><span class="mord">n</span><span class="mord">k</span><span class="mord">s</span><span class="mord">]</span><span class="mord">(</span><span class="mord">n</span><span class="mord">o</span><span class="mord">t</span><span class="mord mspace"> </span><span class="mord" style="margin-right:0.01389em;">w</span><span class="mord">o</span><span class="mord">r</span><span class="mord">k</span><span class="mord">s</span><span class="mord">)</span></span></span></span></span></p>
````

Renderer method: `katex (source)`

### Colored text

Supported all CSS-compatible color notations:

 *  `#rrggbb`
 *  `#rgb`
 *  `rgb(r, g, b)`
 *  `rgb(r%, g%, b%)`
 *  `rgba(r, g, b, a)`
 *  `rgba(r%, g%, b%, a)`
 *  `hsl(h, s, l)`
 *  `hsl(h%, s%, l%)`
 *  `hsla(h, s, l, a)`
 *  `hsla(h%, s%, l%, a)`
 *  `colorName`

#### Text color

Source:

````markdown
%red Red text%
````

Result:

````html
<p><span style="color:red">Red text</span></p>
````

Renderer method: `color (color, text)`

#### Background color

Source:

````markdown
!%yellow Yellow background%
````

Result:

````html
<p><span style="background-color:yellow">Yellow background</span></p>
````

Renderer method: `bg (color, text)`

### Ruby (furigana) and glosses

### Ruby

Source:

````markdown
{東}(とう){方}(とう)
````

Result:

````html
<p><ruby>東<rt>とう</rt></ruby><ruby>方<rt>とう</rt></ruby></p>
````

Renderer method: `ruby (color, ruby)`

### Glosses

Source:

````markdown
!{Word or phrase}(Pop-up description)
````

Result:

````html
<p><span class="gloss" title="Pop-up description">Word or phrase</span></p>
````

Renderer method: `gloss (color, gloss)`

NB: By default, HTML in glosses not supported. You may override default renderer method
for supporting custom pop-ups.

### Empty anchors

Source:

````markdown
 *  Item 1
 *  Item 2
 *  ^item-3 Item 3
     *  Item 3.1
     *  Item 3.2
````

Result:

````html
<ul>
<li>Item 1</li>
<li>Item 2</li>
<li><span id="item-3" />Item 3<ul>
<li>Item 3.1</li>
<li>Item 3.2</li>
</ul>
</li>
</ul>
````

Renderer method: `anchor (id)`

## TODO

 *  Hashtags: `#hashtag`.
 *  Total rewrite with XRegExp library.
 *  Add support for Japanese punctuation.

## Usage

Minimal usage:

```js
var marked = require('marked-pax');
console.log(marked('I am using __markdown__.'));
// Outputs: <p>I am using <strong>markdown</strong>.</p>
```

Example setting options with default values:

```js
var marked = require('marked-pax');
marked.setOptions({
  renderer: new marked.Renderer(),
  gfm: true,
  tables: true,
  breaks: false,
  pedantic: false,
  sanitize: true,
  smartLists: true,
  smartypants: false
});

console.log(marked('I am using __markdown__.'));
```

### Browser

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>Marked in the browser</title>
  <script src="lib/marked-pax.js"></script>
</head>
<body>
  <div id="content"></div>
  <script>
    document.getElementById('content').innerHTML =
      marked('# Marked in browser\n\nRendered by **marked**.');
  </script>
</body>
</html>
```

Result:

````html
<h1 id="chapter-1">Случайный Юпитер — актуальная национальная задача</h1>
<h1 id="chapter2">Астероидный возмущающий фактор в XXI веке</h1>
````

# marked

> A full-featured markdown parser and compiler, written in JavaScript. Built
> for speed.

## marked(markdownString [,options] [,callback])

### markdownString

Type: `string`

String of markdown source to be compiled.

### options

Type: `object`

Hash of options. Can also be set using the `marked.setOptions` method as seen
above.

### callback

Type: `function`

Function called when the `markdownString` has been fully parsed when using
async highlighting. If the `options` argument is omitted, this can be used as
the second argument.

## Options

### highlight

Type: `function`

A function to highlight code blocks. The first example below uses async highlighting with
[node-pygmentize-bundled][pygmentize], and the second is a synchronous example using
[highlight.js][highlight]:

```js
var marked = require('marked');

var markdownString = '```js\n console.log("hello"); \n```';

// Async highlighting with pygmentize-bundled
marked.setOptions({
  highlight: function (code, lang, callback) {
    require('pygmentize-bundled')({ lang: lang, format: 'html' }, code, function (err, result) {
      callback(err, result.toString());
    });
  }
});

// Using async version of marked
marked(markdownString, function (err, content) {
  if (err) throw err;
  console.log(content);
});

// Synchronous highlighting with highlight.js
marked.setOptions({
  highlight: function (code) {
    return require('highlight.js').highlightAuto(code).value;
  }
});

console.log(marked(markdownString));
```

#### highlight arguments

`code`

Type: `string`

The section of code to pass to the highlighter.

`lang`

Type: `string`

The programming language specified in the code block.

`callback`

Type: `function`

The callback function to call when using an async highlighter.

### renderer

Type: `object`
Default: `new Renderer()`

An object containing functions to render tokens to HTML.

#### Overriding renderer methods

The renderer option allows you to render tokens in a custom manor. Here is an
example of overriding the default heading token rendering by adding an embedded anchor tag like on GitHub:

```javascript
var marked = require('marked');
var renderer = new marked.Renderer();

renderer.heading = function (text, level) {
  var escapedText = text.toLowerCase().replace(/[^\w]+/g, '-');

  return '<h' + level + '><a name="' +
                escapedText +
                 '" class="anchor" href="#' +
                 escapedText +
                 '"><span class="header-link"></span></a>' +
                  text + '</h' + level + '>';
},

console.log(marked('# heading+', { renderer: renderer }));
```
This code will output the following HTML:
```html
<h1>
  <a name="heading-" class="anchor" href="#heading-">
    <span class="header-link"></span>
  </a>
  heading+
</h1>
```

#### Block level renderer methods

- code(*string* code, *string* language)
- blockquote(*string* quote)
- html(*string* html)
- heading(*string* text, *number*  level)
- hr()
- list(*string* body, *boolean* ordered)
- listitem(*string*  text)
- paragraph(*string* text)
- table(*string* header, *string* body)
- tablerow(*string* content)
- tablecell(*string* content, *object* flags)

`flags` has the following properties:

```js
{
    header: true || false,
    align: 'center' || 'left' || 'right'
}
```

#### Inline level renderer methods

- strong(*string* text)
- em(*string* text)
- codespan(*string* code)
- br()
- del(*string* text)
- link(*string* href, *string* title, *string* text)
- image(*string* href, *string* title, *string* text)

### gfm

Type: `boolean`
Default: `true`

Enable [GitHub flavored markdown][gfm].

### tables

Type: `boolean`
Default: `true`

Enable GFM [tables][tables].
This option requires the `gfm` option to be true.

### breaks

Type: `boolean`
Default: `false`

Enable GFM [line breaks][breaks].
This option requires the `gfm` option to be true.

### pedantic

Type: `boolean`
Default: `false`

Conform to obscure parts of `markdown.pl` as much as possible. Don't fix any of
the original markdown bugs or poor behavior.

### sanitize

Type: `boolean`
Default: `false`

Sanitize the output. Ignore any HTML that has been input.

### smartLists

Type: `boolean`
Default: `true`

Use smarter list behavior than the original markdown. May eventually be
default with the old behavior moved into `pedantic`.

### smartypants

Type: `boolean`
Default: `false`

Use "smart" typograhic punctuation for things like quotes and dashes.

## Access to lexer and parser

You also have direct access to the lexer and parser if you so desire.

``` js
var tokens = marked.lexer(text, options);
console.log(marked.parser(tokens));
```

``` js
var lexer = new marked.Lexer(options);
var tokens = lexer.lex(text);
console.log(tokens);
console.log(lexer.rules);
```

## CLI

``` bash
$ marked -o hello.html
hello world
^D
$ cat hello.html
<p>hello world</p>
```

## Philosophy behind marked

The point of marked was to create a markdown compiler where it was possible to
frequently parse huge chunks of markdown without having to worry about
caching the compiled output somehow...or blocking for an unnecesarily long time.

marked is very concise and still implements all markdown features. It is also
now fully compatible with the client-side.

marked more or less passes the official markdown test suite in its
entirety. This is important because a surprising number of markdown compilers
cannot pass more than a few tests. It was very difficult to get marked as
compliant as it is. It could have cut corners in several areas for the sake
of performance, but did not in order to be exactly what you expect in terms
of a markdown rendering. In fact, this is why marked could be considered at a
disadvantage in the benchmarks above.

Along with implementing every markdown feature, marked also implements [GFM
features][gfmf].

## Benchmarks

node v0.8.x

``` bash
$ node test --bench
marked completed in 3411ms.
marked (gfm) completed in 3727ms.
marked (pedantic) completed in 3201ms.
robotskirt completed in 808ms.
showdown (reuse converter) completed in 11954ms.
showdown (new converter) completed in 17774ms.
markdown-js completed in 17191ms.
```

__Marked is now faster than Discount, which is written in C.__

For those feeling skeptical: These benchmarks run the entire markdown test suite 1000 times. The test suite tests every feature. It doesn't cater to specific aspects.

### Pro level

You also have direct access to the lexer and parser if you so desire.

``` js
var tokens = marked.lexer(text, options);
console.log(marked.parser(tokens));
```

``` js
var lexer = new marked.Lexer(options);
var tokens = lexer.lex(text);
console.log(tokens);
console.log(lexer.rules);
```

``` bash
$ node
> require('marked').lexer('> i am using marked.')
[ { type: 'blockquote_start' },
  { type: 'paragraph',
    text: 'i am using marked.' },
  { type: 'blockquote_end' },
  links: {} ]
```

## Running Tests & Contributing

If you want to submit a pull request, make sure your changes pass the test
suite. If you're adding a new feature, be sure to add your own test.

The marked test suite is set up slightly strangely: `test/new` is for all tests
that are not part of the original markdown.pl test suite (this is where your
test should go if you make one). `test/original` is only for the original
markdown.pl tests. `test/tests` houses both types of tests after they have been
combined and moved/generated by running `node test --fix` or `marked --test
--fix`.

In other words, if you have a test to add, add it to `test/new/` and then
regenerate the tests with `node test --fix`. Commit the result. If your test
uses a certain feature, for example, maybe it assumes GFM is *not* enabled, you
can add `.nogfm` to the filename. So, `my-test.text` becomes
`my-test.nogfm.text`. You can do this with any marked option. Say you want
line breaks and smartypants enabled, your filename should be:
`my-test.breaks.smartypants.text`.

To run the tests:

``` bash
cd marked/
node test
```

### Contribution and License Agreement

If you contribute code to this project, you are implicitly allowing your code
to be distributed under the MIT license. You are also implicitly verifying that
all code is your original work. `</legalese>`

## License

Copyright (c) 2011-2014, Christopher Jeffrey. (MIT License)

See LICENSE for more info.

[gfm]: https://help.github.com/articles/github-flavored-markdown
[gfmf]: http://github.github.com/github-flavored-markdown/
[pygmentize]: https://github.com/rvagg/node-pygmentize-bundled
[highlight]: https://github.com/isagalaev/highlight.js
[badge]: http://badge.fury.io/js/marked
[tables]: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#wiki-tables
[breaks]: https://help.github.com/articles/github-flavored-markdown#newlines
