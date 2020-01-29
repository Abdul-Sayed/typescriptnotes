# Using typescript

run `tsc index.ts` ==> to transpile the ts code into a generated index.js file. Re-run the command as the ts file changes
make sure index.js is closed, otherwise vscode shoes duplicate code errors
Typescript will help identify errors during development, before compilation. This avoids JS dynamically setting types at runtime.

in index.html, import index.js. To repaint the html as the js file changes;
    npm init, ok to defaults
    npm install --save-dev lite-server
in package.json, in scripts "start": "lite-server"
    run `npm start` in temrinal

 ## Typescript watchmode
    tsc app.ts --watch or tsc app.ts -w

If multiple ts files are in the directory, import the js version of that file into index.html
`tsc --init`   //==> typescript will manage the current directory and generate a tsconfig.json
then in terminal simply run `tsc -w` - then any ts file thats modified triggers a recompile 

## tsconfig.json

### include/exclude files from compilation 
Here you can configure what files to exclude from compilation. Insert "exclude": { ... } before the close of the JSON object;
    "exclude": [
      "node_modules",
      "**/*.dev.ts"
    ]  
//==> this exludes the node_modules folder and any dev.ts file in any sub folder from the compilation

Alternately you can specify exactly which files/folder to include in the compilation and only they will be watched;
    "include": [
      "app.ts",
      "analytics.ts"
    ]
Typescript looks to compile everything included and subtracts from that anything excluded

### compilerOptions 
Here you can specify the javascript version to compile to. If the "lib" option is not set, then the DOM API is accessable, as part of defaults. If it is commented in, then it will need the following to match the default; 
"lib": [
  "dom",
  "dom.iterable",
  "es6",
  "scripthost"
]
Uncomment `"sourceMap": true` for the ts files to show in Sources tab of chrome dev tools

Uncomment and set `"outDir": "./dist"` to have typescript place compiled javascript in the dist directory. Be sure to update index.html which points to those JS files. 

Uncomment and set `"rootDir": "./src"` to have typescript to only look to the src folder for ts files

Uncomment `"removeComments": true`

`"noEmitOnError": true`  -> ts file will not compile to js if it has an error, even if its valid js code


# Type Declarations

When declaring variables, but not assigning them, state their types. If values are assigned to variables during declaration, typescript will infer their types, so explicitely providing types is redundant.

    let number1: number; ==> type should be provided for declarations. Not assigning a type gives it the default type of :any
    const number2 = 5; ==> dont provide type here, its already inferred

Reassigning a variable a value thats not of the variable's type will throw a type error.
String, boolean, and number (includes integer and floats) are valid primitive types.

When inferring types, const and let are different; (const is a literal type)
const number = 2.8 //=> inferred to be type 2.8 because it cannot be reassigned as a different number
let number2 = 2.8 //=> inferred to be type number because it can change to another number

## Objects

Assigning a variable as an object literal infers it to be not only of type object but also of that specific object, replicated as key:type pairs

    const person = {
      name: 'Max',
      age: 30
    };

This can be written explicitely as;
    const person: { name: string; age: number; } = {
      name: 'Max',
      age: 30
    };

person could not be set to an object of different keys or different value types or different dimensions. 

## Arrays

Array type - specify if it will be an array of strings, numbers, objects, etc
let favorites: string[];
Mixed arrays are inferred by javascript to be a union of multiple types

## Tupule

Arrays of fixed length and type format 
role = [number, string] //=> role[0] = type number, role[1] = type string, role.length = 2
With this, the array can be mutated to change size but not be reassigned an array of different size

let address: [number, string] = [10, 'Elm Street']

## Enum

Assigns labels to numbers, and makes them available as global constants;

    enum Role {ADMIN, READ_ONLY, AUTHOR}; //=> By default, ADMIN == 0, READ_ONLY == 1, AUTHOR == 2

Other numbers can be assigned; enum Role {ADMIN = 5, READ_ONLY, AUTHOR}; and the subsequent labels will be incremented by 1
More customization is also possible; enum Role {ADMIN = 'ADMIN', READ_ONLY = 100, AUTHOR = 'AUTHOR'};

enum Color {
  Gray,   // gets value 0
  Green,  // gets value 1
  Blue  // gets value 2
}

let myColor: Color = Color.Green;      //==> myColor is of type color and gets the value of Color.green
console.log(myColor);   //==> actually logs 1

## Union Types

Used when a variable can be of multiple types

    function combine(input1: number | string, input2: number | string) {
      const result = input1 + input2;
      return result;
    }

Your function may need a run time check when working with ambiguous union types

    function combine(input1: number | string, input2: number | string) {
      let result
      if (typeof input1 === 'number && typeof input2 === 'number) {
      result = input1 + input2;
      } else {
      result = input1.toString() + input2.toString();
      }
      return result;
    }

## literal types

    function combine(
    input1: number | string, 
    input2: number | string, 
    resultConversion: 'as-number' | 'as-string'
    ) {
        let result
        if (typeof input1 === 'number && typeof input2 === 'number || resultConversion === 'as-number') {
        result = +input1 + +input2;    //==> prepending a string with + coerces it to number
        } else {
        result = input1.toString() + input2.toString();
        }
        return result;
    }


    console.log(combine('30', '26', 'as-number'))  //==> 56


## Type Aliases ; Custom Typescript
Typically used to make references to complex union types;

    type Combinable = number | string;
    let input1: Combinable

## Function's return type 
Explicitely state the type of a function's return, rather than leaving it to typescript to infer it;

    function add(n1: number, n2: number): number {
      return n1 + n2;
    }

Do this only if there is a strong reason to do so. 

## Function's argument type

    let combineValues: (a: number, b: number) => number;   
  //==> combineValues is typed as function which takes two parameters of type number and which returns a number

    function add(n1: number, n2: number): number {
      return n1 + n2;
    }
    combineValues = add;

---
function multiply(value1: number, value2: number): number {
  return value1 * value2;
}

console.log(multiply(5, 'Max'))  //==> ts complains beacuse 2nd argument is not a number


### Function Types

    function multiply(value1: number, value2: number): number {
      return value1 * value2;
    }

    let myMultiply: (value1: number, value2: number) => number

    myMultiply = multiply   //==> this is valid because ,ultiply takes two number args and returns a number

---
    function addAndHandle(n1: number, n2: number, log: (num: number) => void) {
      const result = n1 + n2;
      log(result)
    }

    addAndHandle(10, 20, num => console.log(num))

### Void type
Functions with no return statemnt; that don't return anything (ex: functions that only log info) return a void type
These are functions that return undefined

function sayHello(): void {
  console.log('Hello')
}

undefined is also a valid typescript type but type casting a variable to undefined is not useful since later it will be assigned a value. 

## Other Types

### Unknown Type
For when you don't know in advance a variable's type

let userInput: unknown; 
let userName: string;

// userInput gets assigned some value dynamically ... 

if (typeof userInput === 'string') {
  userName = userInput;
}

### Never Type 
Some functions don't need to return anything, they just perform a task 

  function generateError(message: string, code: number): never {
    throw { message: message, errorCode: code };
  }

  const result = generateError('An error occured!', 500);
  console.log(result)   //=> logs nothing, not even undefined


## Working with the DOM

When selecting DOM elements that may not exist (yet), its best to wrap that in an if check, otherwise ts compains that its null. 

const button = document.querySelector('button');

if (button) {
  button.addEventListener('click', () => console.log('clicked'))
}