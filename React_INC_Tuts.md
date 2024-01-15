## Module 1

Javascript library not a framework like angular, vue, ui5
- developed by fb in 2003
- very minimalistic, just focusses on building on efficient UI building
- Need to use other libraries to create fully functioning silgle page application
- React combines HTML and JS logics returning JSX as result.
- React has no provision for global state management - use library
- no routing - use react router for routing
- React has a **Virtual DOM** along with the original DOM, whenever there is a change in the components, 
	- React compares the original and virtual DOM, and only the part that is change is re-rendered instead of re-rendering the whole DOM. 
	- This makes React faster than any other frameworks out there.
- [[Node]] is an event driven **JS runtime** used for building scalable network applications.
- #### **create-react-app:**
	- created by Facebook
	- an environment that comes with preconfigured requisites for building an react application
	- it creates live development server, webpack (module bundler), and BABEL (JS compiler) to automatically compile the JS, HTML, CSS and more importantly the JSX files. 
	- it used **ESLint** to identify and warn about the syntax mistakes during the development process


## Module 2

- package.json contains all the dependencies and scripts required  by the project.
- types of exports - 
	- Named exports: Components that are exported using names - 
		-- `export namedComponent;`
		-- while importing they need to be enclosed in curly braces - 
			`import { namedComponent } from './App.js'`
			`import { namedComponent as temp} from './App`
	- Default exports: keyword default is used while exporting them - 
		-- `export default namedComponent`
		-- while importing - `import nameedComponent from './App'`
- it is not necessary for adding extensions while importing items from JS files, but it is absolutely important for other type of files
- use '../' for moving one hierarchy up in file import path.

- #### var, const, let
	- **var**
		1. `var` declarations are globally scoped or function/locally scoped. The scope is global when a `var` variable is declared outside a function and is available for use in the whole window..
		2. var variables can be re-declared and updated.
		3. So `var` variables are hoisted to the top of their scope and **initialized with a value of** `undefined`.
		4. **Major problem** with `var` is that we don't know whether the variable is declared previously or not.
	- **let**
		1. `let` is block scoped, if the same variable is defined in different scopes, there will be no error
		2. Just like  `var`, `let` declarations are hoisted to the top. Unlike `var` which is initialized as `undefined`, the `let` keyword is** not initialized**. So if you try to use a `let` variable before declaration, you'll get a `Reference Error`.
	- **const**
		1. Variables declared with the `const` maintain constant values throughout the block.
		2. `const` declarations are also block scoped.
		3.  like `let`, `const` declarations are hoisted to the top but are not initialized.

#### Referenced, Primitive datatypes 
- JavaScript provides six type of primitive values that includes Number, String, Boolean, Undefined, Symbol, and BigInt. 
- The size of Primitive values are fixed, therefore JavaScript stores the primitive value in the **call stack (Execution context).**
- JavaScript provides three types of Reference values that include Array, Object, and Function. 
- The size of a reference value is dynamic therefore It is stored on **Heap**.

**Therefore, before copying a function, array, object, it is advised to make a deep copy instead of just copying as usual.**
```
let a = [1,2,3,4]
let b = a
b.push('x')
console.log(a,b)
```
value of both a and b change. 

#### Spread and Rest operators
The main difference between rest and spread is that - 
- the rest operator puts the rest of some specific user-supplied values into a JavaScript array. 
- But the spread syntax expands iterables into individual elements.

```
spread operator

let a = [1,2,3,4]
let b = [...a ,6,7,8]

console.log(a,b)

can also be used for deep-copying the elements

for array = let b = [...a]
for object = let b = {...a}

```
```
rest operator

mostly used in variadic functions where you don't know the number of arguments, it converts all the unknown number of variables into iterable array.

function myBio(firstName, lastName, ...otherInfo) { 
  return otherInfo;
}

// Invoke myBio function
myBio("Oluwatobi", "Sofela", "CodeSweetly", "Web Developer", "Male");

//output:
["CodeSweetly", "Web Developer", "Male"]
```

#### Higher Order Functions in JS
- A “higher-order function” is a function that accepts functions as parameters and/or returns a function.
	- **map** - The `.map()` method executes a callback function on each element in an array. It **returns a new array** made up of the return values from the callback function.
	
	- **forEach** - Almost same as `.map()`. The `.forEach()` method executes a callback function on each of the elements in an array in order.  **This method does not return anything**
	
	- **filter** - The `.filter()` method executes a callback function on each element in an array. The callback function for each of the elements must return either `true` or `false`. The **returned array is a new array with any elements for which the callback function returns `true`.**
	
	- **reduce** - The `.reduce()` method iterates through an array and returns a single value. It takes a callback function with two parameters `(accumulator, currentValue)` as arguments. On each iteration, `accumulator` is the value returned by the last iteration, and the `currentValue` is the current element.


## Module 3

- Differentiate the tags from normal html tags, Always use first letter capital for custom JSX tags.
- Difference betwee functional component and class component