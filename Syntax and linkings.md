```toc
```

JavaScript or JS can be executed on any environment having a JS engine, and mostly we use a browser to run the scripts. 

## Linking Js to webpages

 The [[WebWork/WebSangraha/HTML/HTML with JS| HTML `<script>` tag]] is used to define a client-side script (JavaScript). It can be used in `<head>` as well as `<body>` tag.
- **Position is vital as the scripts called in  `<head>` are loaded before rendering of elements and the one in `<body>` are executed after rendering all the elements.** 

## Building blocks of JS

1. **Statements**
	Statements are syntax constructs and commands that perform actions.
	Eg. `alert('Hello user, let's learn Javascript!')`

2. Semicolons
	Semicolons are used to signify an end of statement in all the programming languages. A semicolon may be omitted in most cases when a line break exists, it is automatically assumed/inserted by JS.
	
	**But that is not always the case!!**, Eg. 
	```js
	alert("Hello") 
	[1, 2].forEach(alert);
	
	// This is read as - 
	alert("Hello")[1, 2].forEach(alert);        // Can cause error!!
	```

3. Comments
	It is always suggested to add comments in your code and keep the codebase well documented, especially if it is a moderately large and complex project.
	
	`//` -- is used for single line comments 
	`/**/` -- is used for multi line comments

4. Variables
	Variable are like virtual containers for data in any programming language. Each variable is a pointer to a physical memory space in the operating device.
	
	Read more about working with [[Variables in JS | variables in JS here]]

## Sources

- https://javascript.info/structure