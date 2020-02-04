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

The property:type blueprint of the person object can be written explicitely as;
    const person: { name: string; age: number; } = {
      name: 'Max',
      age: 30
    };

Therefore person could not be set to an object of different keys, value types, or dimensions. 

---
complex object
    let complex: {data: number[], output: (all:boolean) => number[]} = {
      data: [100, 3.99, 10],
      output: (all: boolean) => {
        return data
      }
    }

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

    let myAge: number | string = 27;
    myAge = "27"  //=> ok
    myAge = true  //=> not ok

  

    function combine(input1: number | string, input2: number | string) {
      let result
      if (typeof input1 === 'number' && typeof input2 === 'number') {
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

---

    type Complex = { data: number[]; output: (all: boolean) => number[] }

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


## Other Types

### Void type
Functions with no return statemnt; that don't return anything (ex: functions that only log info) return a void type
These are functions that return undefined

function sayHello(): void {
  console.log('Hello')
}

undefined is also a valid typescript type but type casting a variable to undefined is not useful since later it will be assigned a value. 

### Unknown Type
For when you don't know in advance a variable's type

let userInput: unknown; 
let userName: string;

// userInput gets assigned some value dynamically ... 

if (typeof userInput === 'string') {
  userName = userInput;
}

### Never Type 
Some functions never return anything, such as when throwing errors

  function generateError(message: string, code: number): never {
    throw { message: message, errorCode: code };
  }

  const result = generateError('An error occured!', 500);
  console.log(result)   //=> logs nothing, not even undefined

### Nullable Types 
If you set "strictNullChecks": true in compilerOptions of tsconfig, ts complains if anything but undefiend is set to null

    let canBeNull = 12;
    canBeNull = null;   //=> will alert an error; type number cannot be reset to type null
    let canAlsoBeNull;
    canAlsoBeNull = null;   //=> no problem because type any can be set to type null
    let canThisBeAny:null = null;
    canThisBeAny = 12;    //=> will alert an error; type number cannot be assigned to type null
## Working with the DOM

When selecting DOM elements that may not exist (yet), its best to wrap that in an if check, otherwise ts compains that its null. 

const button = document.querySelector('button');

if (button) {
  button.addEventListener('click', () => console.log('clicked'))
}


## Example 

    type BankAccount = { money: number; deposit: (value: number) => void };

    let bankAccount: BankAccount = {
      money: 2000,
      deposit(value: number): void {
        this.money += value;
      }
    };

    let myself: {
      name: string;
      bankAccount: BankAccount;
      hobbies: string[];
    } = {
      name: "Max",
      bankAccount: bankAccount,
      hobbies: ["Sports", "Cooking"]
    };

    myself.bankAccount.deposit(3000);



## Typescript classes  

class Car {
  name: string;
  acceleration: number = 0;
  
  constructor(name: string) {
    this.name = name;
  }

  honk() {
    console.log("Toooooooooot!");
  }

  accelerate(speed: number) {
    this.acceleration = this.acceleration + speed;
  }
}

var car = new Car("BMW");
console.log(car.acceleration);
car.accelerate(10);
console.log(car.acceleration);

----
class Person {
  constructor(
    public name: string,
    protected age: number,
    private genderType: string
  ) {
    this.name = name;
    this.age = age;
  }

  printAge() {
    console.log(this.age);
  }

  private setType(type: string) {
    this.genderType = type;
    console.log(this.genderType);
  }

  descriptiveComment(description: string) {
    this.setType(description);
  }
}

const person = new Person("Max", 29, "male");

console.log(person.name); //=> "Max"
// person.age; //=> unaccesible, since protected variables are only accessable within the Class, and classes that inherit from it
// person.genderType; //=> unaccesable, since private variables are only accessable within the Class
person.printAge(); //=> 29
// person.setType("fluid");  //=> unaccessable
person.descriptiveComment("fluid");

class Max extends Person {
  constructor(name: string, age: number, genderType: string) {
    super(name, age, genderType);
  }
}

const max = new Max("Max", 32, "fagh");
console.log(max);

## Inheritance v

class baseObject {
  constructor(public width: number, public length: number) {
    this.width = width;
    this.length = length;
  }
}

class Rectangle extends baseObject {
  calcSize() {
    return this.width * this.length;
  }
}

const rectangle = new Rectangle(5, 2);

console.log(rectangle.calcSize());

## Getters / Setters 

    class Plant {
      private _species: string = "Green Plant";

      get species() {
        return this._species;
      }

      set species(value: string) {
        this._species = value;
      }
    }

    let plant = new Plant();

    console.log(plant.species);  // Green Plant

    plant.species = "fern";

    console.log(plant.species); // fern


## Static properties 
	class CommonMath {
    static PI = 3.14;

    static Circumference(diameter) {
      return diameter * this.PI;
    }
	}

    CommonMath.Pi   // 3.14
    CommonMath.Circumference(2)  // 6.28;


## Abstract classes - base classes that cannot be instantiated, can only be inherited
Used to make a generalized blueprint that wont be used other than making other more specialized blueprints

    abstract class Project {
      projectName: string = "Default";
      budget: number = 1;

      abstract changeName(name: string): void;    // abstract methods only define the parameter and return type. Inheriting classes will need to implement the method on their own. 

      calcBudget() {         // normal methods are inherited, and dont need to be re-implemented
        return this.budget * 2;
      }
    }

    class ITProject extends Project {
      changeName(name: string) {
        this.projectName = name;
      }
    }

    let newProject = new ITProject;
    newProject.changeName("Angular MEAN proj");
    newProject.budget = 1000;

    console.log(newProject);



## Singleton Pattern
Object Oriented Singleton Pattern - create a class instance once only, and subsequently reference that same instance
Useful for preserving a datastructure across a whole application
class OnlyOne {
  private static instance: OnlyOne;
  private constructor(public name: string) {
    this.name = name;
  }
  static getInstance() {
    if (!OnlyOne.instance) {
      OnlyOne.instance = new OnlyOne('The Only One');
    }
    return OnlyOne.instance;
  }
}

let wrong = new OnlyOne('The Only One');   // cannot make an instance of this class
let right = OnlyOne.getInstance();   // after this first time, no instances can be created

## readonly properties

To make properties readonly, you can make them private and provide them via a getter method. Another way is to simply designate them as readonly; `public readonly na,e: string`


# Modules  

## Namespaces 

  When functions can be grouped into the same category - such as math functions. Namespace properties that are exported can be consumed from outside;

    namespace MyMath {
      const PI = 3.14;

      export function calculateCircumference(diameter: number) {
        return diameter * PI;
      }

      export function calculateRectangle(width: number, length: number) {
        return width * length;
      }
    }

    console.log(MyMath.calculateCircumference(10));
    console.log(MyMath.calculateRectangle(5, 2))
