JavaScript (JS) is a versatile scripting language that can be executed in various environments featuring a JS engine. The most common use is within web browsers, where it enables dynamic and interactive content. This documentation provides an overview of linking JS to webpages and essential building blocks of JS.

- [[#Linking JS to Webpages|Linking JS to Webpages]]
	- [[#Linking JS to Webpages#Example: Linking JS in the `<head>`|Example: Linking JS in the `<head>`]]
	- [[#Linking JS to Webpages#Example: Linking JS in the `<body>`|Example: Linking JS in the `<body>`]]
- [[#Building Blocks of JS|Building Blocks of JS]]
	- [[#Building Blocks of JS#1. Statements|1. Statements]]
	- [[#Building Blocks of JS#2. Semicolons|2. Semicolons]]
	- [[#Building Blocks of JS#3. Comments|3. Comments]]
	- [[#Building Blocks of JS#4. Variables|4. Variables]]
- [[#Sources|Sources]]

## Linking JS to Webpages

To integrate JavaScript into webpages, the HTML `<script>` tag is employed. It can be placed in both the `<head>` and `<body>` sections of an HTML document.

- **Positioning Considerations:**
  The placement of scripts is crucial. Those in the `<head>` section are loaded before elements are rendered, while those in the `<body>` section execute after rendering all elements.

### Example: Linking JS in the `<head>`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JS Linking Example</title>
    <!-- JS in head -->
    <script src="script.js"></script>
</head>
<body>
    <!-- Body content -->
</body>
</html>
```

### Example: Linking JS in the `<body>`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JS Linking Example</title>
</head>
<body>
    <!-- Body content -->
    <h1>Hello, this is the body of the document.</h1>

    <!-- JS in body -->
    <script src="script.js"></script>
</body>
</html>
```

## Building Blocks of JS

### Statements

Statements are fundamental syntax constructs and commands that perform actions within JS. For example:

```javascript
alert("Hello user, let's learn JavaScript!");
```

### Semicolons

Semicolons signify the end of statements in programming languages. While JS often infers line breaks as statement separators, it's recommended to use semicolons to avoid potential issues. For instance:

```javascript
alert("Hello");
[1, 2].forEach(alert); // Caution: Can cause errors without semicolons
```

**Buggy Code Snippet:**

```javascript
alert("Hello")
[1, 2].forEach(alert); // This may cause errors without semicolons
```

### Comments

Comments enhance code readability and understanding. Single-line comments use `//`, and multi-line comments use `/**/`. For example:

```javascript
// This is a single-line comment

/*
  This is a
  multi-line comment
*/
```

### Variables

Variables in JS serve as containers for data. They act as pointers to memory spaces in the device. It's essential to understand and use variables for effective programming.

Learn more about [[01. Intro to Variables|Variables in JS]].

## Sources

- [JavaScript.info](https://javascript.info/structure)