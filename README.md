# Argon parser
(A full introduction and documentation will (hopefully) soon be delivered on my website)

---
## Introduction
While converting a large documentation into a JSON format, I was quickly getting tired of writing the large amount of HTML tags by hand. That's why I began thinking of a simple, sophisticated syntax for writing HTML snippets which should increase the productivity by being very easy to write while decreasing tiring boilerplate, being only as minimal as needed to be distinguishable and efficient - and it's here: _Argon_

---
## Getting started
### Via Node.js
Argon is present [as a module on](https://www.npmjs.com/package/argon-parser) [npm](https://www.npmjs.com) to be used with [Node.js](https://nodejs.org). To get it as dependency, you install it into your project:
```
npm install argon-parser
```
Obviously, [Yarn](https://yarnpkg.com) works too as it uses the npm package registry for its packages as well:
```
yarn add argon-parser
```
### In the browser
Argon can also be used directly in the browser.
#### Automatic fetching
One way to do that is to fetch the script from its repository's website directly in your HTML file:
```
<script src="https://hallo89.github.io/argon-parser/argon.js"></script>
```
This allows you to always have the latest version when it gets pushed to GitHub. However, as this doesn't utilize [SemVer](https://semver.org) processing, it has a chance to break but this is not very likely as the syntax is pretty much stable already.
#### Downloading it
The other way is, well, download the script from its repo and include it with a script in your HTML file.. That was easy.

---
After having imported the script, you can just go ahead and parse your favorite Argon syntax ([documented below](#documentation)) by calling the `argon` function. For example: `argon("This is s//probably em<//most likely//> useful")` (outputs `This is <s>probably</s> <em>most likely</em> useful`).<br>
Note that Argon is only made for HTML _snippets_, for example for small segments of formatted text in a database (like the example in the introduction)!<br>
Also note that Argon does not compile into actual elemented HTML, it just converts a string into a different string - Use innerHTML or insertAdjacentHTML for that.

---
## Documentation
Argon processes a special syntax into a HTML string with the `argon` function - Here's how that syntax looks:
### Simple enclosing tags
```
div<//inner html content//>
```
-> output: `<div>inner html content</div>`
### One word enclosing tags
```
This is strong//Argon parser
```
-> output: `This is <strong>Argon</strong> parser`
- The single word content is stopped by whitespace or any of `. , ? !`
### Non-closing tags
```
Let's get to a/br!//new line!
```
-> output: `Let's get to a<br>new line!`
### Tag rules
- Tag names may only contain letters (e.g. `a`) and hyphens (`-`), but no hyphen at the beginning
### Combining single enclosing tags
- Using a plus (`+`) sign, one word enclosing tags can be subsequently nested
- This, beside being simply more convenient, allows for accessing the inner content when using implicit anchorization (with the special hash attribute case)
```
this is em+strong//important!
```
-> `this is <em><strong>important</strong></em>!`

A more advanced example with attributes and anchorization (see below):
```
Check out a#!:class(anchor)+strong:class(bold)//github.com/Hallo89/argon-parser.
```
-> `Check out <a href="https://github.com/Hallo89/argon-parser" class="anchor"><strong class="bold">github.com/Hallo89/argon-parser</strong></a>.`
### Subsequent characters
- To have a tag directly follow a word, you use a vertical bar (`|`) in between them:
```
Very|strong//important|text
```
-> `Very<strong>important</strong>text`
### Attributes
- The beginning of an attribute is marked by a colon (`:`), followed by an attribute name, which is directly followed by a set of round or square brackets containing the attribute value
- An attribute name must only consist of letters or hyphens
- When needing a round bracket as part of a value, square brackets must be used and vice versa
- The brackets along with their value may be omitted
```
strong:class(bold ag):id(char-3):onclick[log('content')]:contenteditable//Argon
```
-> `<strong class="bold ag" id="char-3" onclick="log('content')" contenteditable>Argon</strong>`
### Special attribute case: href
- Using a hash character (`#`) immediately after the tag name (before potential attributes) unlocks a more convenient way of defining a `href` attribute with special values
- A value may be written after the hash, but can be omitted - entering a value makes the `href` correspond to that value, omitting it will take the tag value instead. There are multiple ways of processing the attribute:
#### Anchorization
- Anchorization is the default behaviour of the hash syntax: it converts a value into a link to a local id

Using an explicit value:
```
A property - see a#property//here!
```
-> `A property - see <a href="#property">here</a>!`

The implicit way:
```
A property - see a#//property!
```
-> `A property - see <a href="#property">property</a>!`
#### Transferprotocolization
- This wonderful name refers to the hash syntax converting a value automatically into a link (with its hypertext transfer protocol (http))
- This is done by appending a exclamation or question mark, for https and http respectively, to the hash character:

With an explicitly defined URL:
```
More info on a#!hallo89.net<//my website//>!
```
-> `More info on <a href="https://hallo89.net">my website</a>!`
```
Unsecure info on a#?hallo89.net<//my website//>...
```
-> `Unsecure info on <a href="http://hallo89.net">my website</a>...`

And implicitly:
```
a#!//hallo89.net, that's my website
```
-> `<a href="https://hallo89.net">hallo89.net</a>, that's my website`
```
a#?//hallo89.net, that's my unsecure website
```
-> `<a href="http://hallo89.net">hallo89.net</a>, that's my unsecure website`

---
- An explicit value may not contain any of following characters: whitespace or any of `,?!:/` (The slash doesn't make much sense here, I will see about removing it from the exceptions)
- For using _any_ character in the value, a round or square bracket may be used. This is like with attributes, the bracket type itself may not be part of the value: when needing a round bracket in a value, use square brackets and vice versa<br>

```
a#(weird::id/syntax!)//property
```
-> `<a href="#weird::id/syntax!">property</a>`
