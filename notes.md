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

Important compilation settings:
    "module": "system"
    "outFile": "./dist/index.js"
    "outDir": "./dist"
    "rootDir": "./src"
    and run tsc -w in terminal

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


const heights: { [key: string]: number } = {"John": 67, "paul": 83}

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

    let averages: number[] = [75, 89, 84];
    averages.push(92)

    const testResults: Array<number> = [1.94, 2.33]    //=> Alternative array of only type number
    testResults.push(-2.9)   // ok
    testResults.push("hell")   // breaks

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


## Interfaces  

When passing objects as a function argument, the entire passed object needs to be type casted;

    function greet(person: { name: string; age: number }): void {
      console.log(`Hello ${person.name}`)
    }
    const person = {
      name: "Maxx",
      age: 27
    }
    greet(person)


Instead of typecasting the entire object, only the property the function needs can be casted.
When passing large objects, this is clearly advantageus. Only type cast the properties used.

    function greet(person: { name: string }): void {
      console.log(`Hello ${person.name}`)
    }
    const person = {
      name: "Maxx",
      age: 27
    }
    greet(person)


If certain object properties are being used often, an Interface can be created to represent them when passing types to functions. 
Only the object and the interface need to be updated during changes, and the functions can be guaranteed certain properties are available
    interface NamedPerson {
      name: string
    }
    function greet(person: NamedPerson): void {
      console.log(`Hello ${person.name}`)
    }
    const person = {
      name: "Maxx",
      age: 27
    }
    greet(person)

Note: Typescript complains if you pass an object literal that contains extra properties directly to the function. A reference to an object is fine.
Interfaces can be updated to take on additional opional properties;
    interface NamedPerson {
      name: string,
      age?: number
    }

### Interfaces with dynamic data
If you want to set up an interface for dynamic data where the properties are not known in advance, use:
    interface NamedPerson {
      name: string,
      age?: number,
      [propName: string]: any
    }
    function greet(person: NamedPerson): void {
      console.log(`Hello ${person.name}`)
    }
    const person = {
      name: "Maxx",
      age: 27,
      hobbies: ['cooking', 'reading']
    }
    greet(person)

### Interface Methods: 

    interface NamedPerson {
      name: string;
      age?: number;
      [propName: string]: any;
      greet(nickName: string): void;
    }

    const person = {
      name: "Maxx",
      age: 27,
      hobbies: ['cooking', 'reading'],
      greet(nickName: string): void {
        console.log(`Well hello there ${nickName}`)
      }
    }

    function greet(person: NamedPerson): void {
      console.log(`Hello ${person.name}`)
    }


    greet(person)  // Hello Maxx

    person.greet('Anna');  // Well hello there Anna

### Interfaces with classes 

    interface NamedPerson {
      name: string;
      age?: number;
      [propName: string]: any;
      greet(nickName: string): void;
    }


    class Person implements NamedPerson {
      constructor(public name: string) {
        this.name = name
      }
      greet(nickName: string): void {
        console.log(`Well hello there ${nickName}`)
      }
    }

    const myPerson = new Person("Maxx");
    myPerson.greet("Anna");   //=> Well hello there Anna


    // fuctions that expect an interface can also be passed a class which implements the interface
    function greet(person: NamedPerson): void {
      console.log(`Hello ${person.name}`)
    }

    greet(myPerson)  // Hello Maxx

### Interface Inheritance 
Object based interfaces can be inherited and expanded upon. 

    interface AgedPerson extends NamedPerson {
      wealth: number;
    }

    // All the (required) properties of the original interface must be re-written
    const oldPerson: AgedPerson = {
      name: 'Max',
      greet(name: string): void {
        console.log(`Well hello there ${name}`)
      },
      wealth: 9999999
    }
 

### Functional Interfaces 
In addition to objects, interfaces can serve as blueprints for function  

    interface DoubleValueFunc {
      (number1: number, number2: number): number;
    }
    let myDoubleFunction: DoubleValueFunc = function (value1: number, value2: number) {
      return (value1 + value2) * 2;
    }
    console.log(myDoubleFunction(10, 20));  //==> 60

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


### Multiple Files 
For larger applications, multiple namespaces accross multiple files are common. 
If the above namespace were two files; 
circleMath.ts :
    namespace MyMath {
      const PI = 3.14;
      export function calculateCircumference(diameter: number) {
        return diameter * PI;
      }
    }
rectangleMath.ts :
    namespace MyMath {
      const PI = 3.14;
      export function calculateRectangle(width: number, length: number) {
        return width * length;
      }
    }
Namespaces can then be inported into index.ts as follows;
    /// <reference path="circleMath.ts" />
    /// <reference path="rectangleMath.ts" />

In tsconfig.json, set 
    "module": "system"
    "outFile": "./dist/index.js"
    "outDir": "./dist"
    "rootDir": "./src"
    and run tsc -w

Also (but less preferred way):
Typescript can compile multiple ts files into a single js file (app.js). Order wrtten is the order the files are bundled together. 
`tsc --outFile dist/app.js src/circleMath.ts src/rectangleMath.ts src/index.ts`

### NEsted Namespaces 

If a namespace in circleMath.ts is:
    namespace MyMath {
      export namespace Circle {
        const PI = 3.14;
        export function calculateCircumference(diameter: number) {
          return diameter * PI;
        }
      }
    }

Access it in index.ts as:
/// <reference path="circleMath.ts" />
console.log(MyMath.Circle.calculateCircumference(10));
OR setup an alias for the nested fuction
import CircleMath = MyMath.Circle;
console.log(CircleMath.calculateCircumference(10));

# Modules  

When there are many namepaces, its  a drawback and you must imperitively export and reference each function being used. It becomes unclear which ts file has which namepace dependency. Modules are declarative about which file has which module dependency which keeps its own scope (doesn't bleed into global scope). The problem is that a module loader has to be setip. 

In circle.ts  
    export const PI = Math.PI;

    export function calculateCircumference(diameter: number) {
      return diameter * PI;
    }

In rectangle.ts 
    export default function calculateRectangle(width: number, length: number) {
      return width * length;
    }  //==> use default if a file exports only one function or property

In index.ts 
    import { PI, calculateCircumference } from "./circle";
    // import * as circle from './circle';    ==> alternative

    import calculateRectangle from "./rectangle";


    console.log(calculateCircumference(10))
    // console.log(circle.calculateCircumference(10))   ==> alternative

    console.log(calculateRectangle(5, 2)); 



## Generic Functions 
Generics are a flexible way to create functions whose parameter type is cast to the type of supplied argument (parameterize types). In other words, the same generic function can be used with different argument types and not break. 
 
    function echo<Type>(data: Type): Type {
      return data
    }

// the agrument types will be inferred but can be made explicit as <string> or <number>
    let foo = echo<string>("FUBAR")    
    let bar = echo<number>(27)

### Generic Types 

// const echo2 is a function that accepts a parameter of type T and its return is type T, and is then assigned echo which has that pattern
    const echo2: <T>(data: T) => T = echo;
    console.log(echo2<string>("Something"));

### Generic with array argument 

    function printAll<T>(args: T[]) {
      args.forEach(e => console.log(e))
    }

    printAll<string>(["apple", "banana"])





### Generics with classes 

    class SimpleMath<T extends number | string, U extends number | string> {
      baseValue: T;
      multiplyValue: U;
      calculate(): number {
        return +this.baseValue * +this.multiplyValue;
      }
    }

    const simpleMath = new SimpleMath<number, string>();

    simpleMath.baseValue = 10;
    simpleMath.multiplyValue = "20";
    console.log(simpleMath.calculate())


    class Person {
      firstName: string;
      lastName: string;

      constructor(firstName: string, lastName: string) {
        this.firstName = firstName;
        this.lastName = lastName;
      }

      getFullName() {
        return this.firstName + " " + this.lastName;
      }
    }

    class Admin extends Person { }

    let admin = new Admin('admin', 'boss');


    // Normal way to set up the function to expect and return type class Person
    // function personEcho(person: Person): Person {
    //   return person;
    // }

    // set this up to be a generic where T is a placeholder for any class that extends person
    function personEcho<T extends Person>(person: T): T {
      return person;
    }


    // let foo = personEcho(admin);   // If personEcho is typed to Person, foo will be of type Person
    let foo = personEcho(admin);  // If personEcho is typed to <T>, foo will be of type Admin

    console.log(foo); // Admin {firstname: 'admin', lastName: 'boss'}

    console.log(personEcho(55))  // Argument of type 55 is not assignable to parameter of type Person


  // example  

      class MyMap<T extends number | string> {
      private map: { [key: string]: T } = {}

      setItem(key: string, item: T): void {
        this.map[key] = item
      }

      getItem(key: string) {
        return this.map[key]
      }

      clear() {
        this.map = {}
      }

      printMap() {
        for (let key in this.map) {
          console.log(`${key} : ${this.map[key]}`)
        }
      }

    }

    const numberMap = new MyMap<number>();
    numberMap.setItem('apples', 5);
    numberMap.setItem('bananas', 10);
    numberMap.printMap();

    const stringMap = new MyMap<string>();
    stringMap.setItem('name', "Max");
    stringMap.setItem('age', "27");
    stringMap.printMap();


## Decorators 

    // Decorators are functions that can be attached to classes, adding functionality. They take the class constructor as the parameter. 

    function logged(constructorFn: Function) {
      console.log(constructorFn)
    }

    @logged
    class Person {
      constructor() {
        console.log("Hi")
      }
    }


    //Factory
    function logging(value: boolean) {
      return value ? logged : null;
    }

    @logging(true)
    class Car {

    }


    //Advanced 

    function printable(constructorFn: Function) {
      constructorFn.prototype.print = function () {
        console.log(this);
      }
    }

    @logging(true)
    @printable
    class Plant {
      name = "Green Plant"
    }

    const plant = new Plant();

    (<any>plant).print()

    // Class Decorators
    // Method Decorator  and Property Decorators
    function editable(value: boolean) {
      return function (target: any, propName: string, descriptor: PropertyDescription) {
        descriptor.writable = value;
      }
    }

    function overwritable(value: boolean) {
      return function (target: any, propName: string): any {
        const newDescriptor: PropertyDescriptor = {
          writable: value
        }
        return newDescriptor;
      }
    }

    class Project {
      @overwritable(false);
      projectName: string;

      constructor(name: string) {
        this.projectName = name;
      }

      @editable(false)
      calcBudget() {
        console.log(1000)
      }
    }

    const project = new Project("Super Project");



    // Parameter Decorator
    function printInfo(target: any, methodName: string, paramIndex: number) {
      console.log("Target:", target)
      console.log("methodName:", methodName)
      console.log("paramIndex:", paramIndex)
    }

    class Course {
      name: string;
      constructor(name: string) {
        this.name = name;
      }
      printStudentNumbers(mode: string, @printInfo printAll: boolean) {
        if (printAll) {
          console.log(10000);
        } else {
          console.log(2000);
        }
      }
    }

    const course = new Course("Super Course");
    course.printStudentNumbers("anything", true);
    course.printStudentNumbers("anything", false);


