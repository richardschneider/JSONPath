# JSONPath Plus [![build status](https://secure.travis-ci.org/s3u/JSONPath.png)](http://travis-ci.org/s3u/JSONPath)

Analyse, transform, and selectively extract data from JSON documents (and JavaScript objects).

# Install

    npm install jsonpath-plus

# Usage

## Syntax

In node.js:

```js
var JSONPath = require('jsonpath-plus');
JSONPath({json: obj, path: path, callback: callback});
```

For browser usage you can directly include `lib/jsonpath.js`, no browserify
magic necessary:

```html
<script src="lib/jsonpath.js"></script>
<script>
    JSONPath({path: path, json: obj, callback: callback, otherTypeCallback: otherTypeCallback});
</script>
```

An alternative syntax is available as:

```js
JSONPath(options, path, obj, callback, otherTypeCallback);
```

The following format is now deprecated:

```js
jsonPath.eval(options, obj, path);
```

## Properties

The properties that can be supplied on the options object or evaluate method (as the first argument) include:

- ***path*** (**required**) - The JSONPath expression as a (normalized or unnormalized) string or array
- ***json*** (**required**) - The JSON object to evaluate (whether of null, boolean, number, string, object, or array type).
- ***autostart*** (**default: true**) - If this is supplied as `false`, one may call the `evaluate` method manually.
- ***flatten*** (**default: false**) - Whether the returned array of results will be flattened to a single dimension array.
- ***resultType*** (**default: "value"**) - Can be case-insensitive form of "value", "path", "pointer", "parent", or "parentProperty" to determine respectively whether to return results as the values of the found items, as their absolute paths, as [JSON Pointers](http://www.rfc-base.org/txt/rfc-6901.txt) to the absolute paths, as their parent objects, or as their parent's property name. If set to "all", all of these types will be returned on an object with the type as key name.
- ***sandbox*** (**default: {}**) - Key-value map of variables to be available to code evaluations such as filtering expressions. (Note that the current path and value will also be available to those expressions; see the Syntax section for details.)
- ***wrap*** (**default: true**) - Whether or not to wrap the results in an array. If `wrap` is set to false, and no results are found, `undefined` will be returned (as opposed to an empty array with `wrap` set to true). If `wrap` is set to false and a single result is found, that result will be the only item returned (not within an array). An array will still be returned if multiple results are found, however.
- ***preventEval*** (**default: false**) - Although JavaScript evaluation expressions are allowed by default, for security reasons (if one is operating on untrusted user input, for example), one may wish to set this option to `true` to throw exceptions when these expressions are attempted.
- ***parent*** (**default: null**) - In the event that a query could be made to return the root node, this allows the parent of that root node to be returned within results.
- ***parentProperty*** (**default: null**) - In the event that a query could be made to return the root node, this allows the parentProperty of that root node to be returned within results.
- ***callback*** (**default: (none)**) - If supplied, a callback will be called immediately upon retrieval of an end point value. The three arguments supplied will be the value of the payload (according to `resultType`), the type of the payload (whether it is a normal "value" or a "property" name), and a full payload object (with all `resultType`s).
- ***otherTypeCallback*** (**default: \<A function that throws an error when @other() is encountered\>**) - In the current absence of JSON Schema support, one can determine types beyond the built-in types by adding the operator `@other()` at the end of one's query. If such a path is encountered, the `otherTypeCallback` will be invoked with the value of the item, its path, its parent, and its parent's property name, and it should return a boolean indicating whether the supplied value belongs to the "other" type or not (or it may handle transformations and return false).

## Instance methods

- ***evaluate(path, json, callback, otherTypeCallback)*** OR ***evaluate({path: \<path\>, json: \<json object\>, callback: \<callback function\>, otherTypeCallback: \<otherTypeCallback function\>})*** - This method is only necessary if the `autostart` property is set to `false`. It can be used for repeated evaluations using the same configuration. Besides the listed properties, the latter method pattern can accept any of the other allowed instance properties (except for `autostart` which would have no relevance here).

## Class properties and methods

- ***JSONPath.cache*** - Exposes the cache object for those who wish to preserve and reuse it for optimization purposes.
- ***JSONPath.toPathArray(pathAsString)*** - Accepts a normalized or unnormalized path as string and converts to an array: for example, `['$', 'aProperty', 'anotherProperty']`.
- ***JSONPath.toPathString(pathAsArray)*** - Accepts a path array and converts to a normalized path string. The string will be in a form like: `$['aProperty']['anotherProperty][0]`. The JSONPath terminal constructions `~` and `^` and type operators like `@string()` are silently stripped.
- ***JSONPath.toPointer(pathAsArray)*** - Accepts a path array and converts to a [JSON Pointer](http://www.rfc-base.org/txt/rfc-6901.txt). The string will be in a form like: `'/aProperty/anotherProperty/0` (with any `~` and `/` internal characters escaped as per the JSON Pointer spec). The JSONPath terminal constructions `~` and `^` and type operators like `@string()` are silently stripped.

# Syntax through examples

Given the following JSON, taken from http://goessner.net/articles/JsonPath/ :

```json
{
  "store": {
    "book": [
      {
        "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      {
        "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99
      },
      {
        "category": "fiction",
        "author": "Herman Melville",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99
      },
      {
        "category": "fiction",
        "author": "J. R. R. Tolkien",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95
    }
  }
}
```

and the following XML representation:

```xml
<store>
    <book>
        <category>reference</category>
        <author>Nigel Rees</author>
        <title>Sayings of the Century</title>
        <price>8.95</price>
    </book>
    <book>
        <category>fiction</category>
        <author>Evelyn Waugh</author>
        <title>Sword of Honour</title>
        <price>12.99</price>
    </book>
    <book>
        <category>fiction</category>
        <author>Herman Melville</author>
        <title>Moby Dick</title>
        <isbn>0-553-21311-3</isbn>
        <price>8.99</price>
    </book>
    <book>
        <category>fiction</category>
        <author>J. R. R. Tolkien</author>
        <title>The Lord of the Rings</title>
        <isbn>0-395-19395-8</isbn>
        <price>22.99</price>
    </book>
    <bicycle>
        <color>red</color>
        <price>19.95</price>
    </bicycle>
</store>
```

Please note that the XPath examples below do not distinguish between
retrieving elements and their text content (except where useful for
comparisons or to prevent ambiguity).

XPath               | JSONPath               | Result                                | Notes
------------------- | ---------------------- | ------------------------------------- | -----
/store/book/author  | $.store.book[*].author | The authors of all books in the store |
//author            | $..author              | All authors                           |
/store/*            | $.store.*              | All things in store, which are its books (a book array) and a red bicycle (a bicycle object).|
/store//price       | $.store..price         | The price of everything in the store. |
//book[3]           | $..book[2]             | The third book (book object)          |
//book[last()]      | $..book[(@.length-1)]<br>$..book[-1:]  | The last book in order.| To access a property with a special character, utilize `[(@['...'])]` for the filter (this particular feature is not present in the original spec)
//book[position()<3]| $..book[0,1]<br>$..book[:2]| The first two books               |
//book/*[self::category\|self::author] or //book/(category,author) in XPath 2.0 | $..book[0][category,author]| The categories and authors of all books |
//book[isbn]        | $..book[?(@.isbn)]     | Filter all books with an ISBN number     | To access a property with a special character, utilize `[?@['...']]` for the filter (this particular feature is not present in the original spec)
//book[price<10]    | $..book[?(@.price<10)] | Filter all books cheaper than 10     |
| //\*[name() = 'price' and . != 8.95] | $..\*[?(@property === 'price' && @ !== 8.95)] | Obtain all property values of objects whose property is price and which does not equal 8.95 |
/                   | $                      | The root of the JSON object (i.e., the whole object itself) |
//\*/\*\|//\*/\*/text()  | $..*                   | All Elements (and text) beneath root in an XML document. All members of a JSON structure beneath the root. |
//*                 | $..                    | All Elements in an XML document. All parent components of a JSON structure including root. | This behavior was not directly specified in the original spec
//*[price>19]/..    | $..[?(@.price>19)]^    | Parent of those specific items with a price greater than 19 (i.e., the store value as the parent of the bicycle and the book array as parent of an individual book) | Parent (caret) not documented in the original spec
/store/*/name() (in XPath 2.0)  | $.store.*~ | The property names of the store sub-object ("book" and "bicycle"). Useful with wildcard properties. | Property name (tilde) is not present in the original spec
/store/book\[not(. is /store/book\[1\])\] (in XPath 2.0) | $.store.book[?(@path !== "$[\'store\'][\'book\'][0]")] | All books besides that at the path pointing to the first | @path not present in the original spec
//book[parent::\*/bicycle/color = "red"]/category | $..book[?(@parent.bicycle && @parent.bicycle.color === "red")].category | Grabs all categories of books where the parent object of the book has a bicycle child whose color is red (i.e., all the books) | @parent is not present in the original spec
//book/*[name() != 'category']     | $..book.*[?(@property !== "category")] | Grabs all children of "book" except for "category" ones  | @property is not present in the original spec
//book/*[position() != 0]    | $..book[?(@property !== 0)] | Grabs all books whose property (which, being that we are reaching inside an array, is the numeric index) is not 0 | @property is not present in the original spec
/store/\*/\*[name(parent::*) != 'book'] | $.store.*[?(@parentProperty !== "book")] | Grabs the grandchildren of store whose parent property is not book (i.e., bicycle's children, "color" and "price") | @parentProperty is not present in the original spec
//book[count(preceding-sibling::\*) != 0]/\*/text() | $..book.*[?(@parentProperty !== 0)]  | Get the property values of all book instances whereby the parent property of these values (i.e., the array index holding the book item parent object) is not 0 | @parentProperty is not present in the original spec
//book/../\*\[. instance of element(\*, xs:decimal)\] (in XPath 2.0) | $..book..\*@number() | Get the numeric values within the book array | @number(), the other basic types (@boolean(), @string()), other low-level derived types (@null(), @object(), @array()), the JSONSchema-added type, @integer(), the type, @other(), to be used in conjunction with a user-defined callback (see `otherTypeCallback`) and the following non-JSON types that can nevertheless be used with JSONPath when querying non-JSON JavaScript objects (@undefined(), @function(), @nonFinite()) are not present in the original spec


Any additional variables supplied as properties on the optional "sandbox" object option are also available to (parenthetical-based) evaluations.

# Potential sources of confusion for XPath users

1. In JSONPath, a filter expression, in addition to its `@` being a
reference to its children, actually selects the immediate children
as well, whereas in XPath, filter conditions do not select the children
but delimit which of its parent nodes will be obtained in the result.
1. In JSONPath, array indexes are, as in JavaScript, 0-based (they begin
from 0), whereas in XPath, they are 1-based.
1. In JSONPath, equality tests utilize (as per JavaScript) multiple equal signs
whereas in XPath, they use a single equal sign.

# Todos

1. Conditionally resolve JSON references/JSON pointers instead or in
addition to raw retrieved data, with options on how deeply nested.
1. Support non-eval version (which supports parenthetical evaluations)
1. Support OR outside of filters (as in XPath `|`).
1. Create syntax to work like XPath filters in not selecting children?
1. Allow for type-based searches to be JSON Schema aware (and allow
retrieval of schema segment for a given JSON fragment)
1. Pull or streaming parser?
1. Allow option for parentNode equivalent (maintaining entire chain of
parent-and-parentProperty objects up to root)
1. Fix in JSONPath to avoid need for `$`?
1. Define any allowed behaviors for: `$.`, `$[0]`, `$.[0]`, or `$.['prop']`
1. Modularize operator detection and usage to allow for
extensibility (at least non-standard ones)?

# Development

Running the tests on node: `npm test`. For in-browser tests:

* Ensure that nodeunit is browser-compiled: `cd node_modules/nodeunit; make browser;`
* Serve the js/html files:

```sh
    node -e "require('http').createServer(function(req,res) { \
        var s = require('fs').createReadStream('.' + req.url); \
        s.pipe(res); s.on('error', function() {}); }).listen(8082);"
```
* To run the tests visit [http://localhost:8082/test/test.html]().


# License

[MIT License](http://www.opensource.org/licenses/mit-license.php).
