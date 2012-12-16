# TypeScript for JavaScripters #

## 1. Introduction ##

Yet another compile to JavaScript language, and it's written by Microsoft, :x yuk!  But wait! It's actually a very compelling solution. 

There are now many languages set-out to become the new JavaScript, or a strong alternative to JS, but TypeScript is in non of those camps.  Instead of leaving JavScript behind, TypeScript is moving it forward by being a superset of JavaScript.
 

### 1.1 JavaScript is TypeScript ###

TypeScript is a "superset" of JavaScript; it extends existing JavaScript with extra syntax and features. Even if you skipped all of the features TypeScript introduced, you could still say that you were writing TypeScript. And yes, you're TypeScriptless JavaScript code will still compile using tsc, even though you're looking at plain JavaScript.  But let's not get silly now...

"So, Microsoft just invented these new features?  I bet it's very .NETish".  And again, we have to give Microsoft some credit here.  The added TypeScript syntax is all based on future/draft JavaScript (EcmaScript) specifications.  This means that the TypeScript you write today will look like the JavaScript you'll be writing tomorrow; it's future proof.

Well that's the theory; we have seen this all before.  Back in 2006, Adobe jumped the gun on implementing and shipping on a draft EcmaScript spec that was untimely dropped. That language was ActionScript 3, the current scripting language of Adobe Flash, and depending on who you speak to, it isn't doing so well these days.  But lucky for us JavaScripters, the "byte-code" we end up with *is* JavaScript, so the risk involved is far lower than Adobe's gamble.


### 1.2 Compiled TypeScript is JavaScript (dur!) ###

When TypeScript is compiled we get regular JavaScript. That compiled JavaScript can be executed in any regular JS environments (browser, Node.js, etc).  But unlike some other "compile to JavaScript" languages, the output from tsc is extremely clean and readable.  In fact, the output from tsc is so clean you could choose to abandon TypeScript halfway through a project and continue using the compiled JavaScript.  We are spared variable names such as $ax_3; what goes in is what goes out.  Code is not minified or obfuscated, and there's no boilerplate abstraction. Instead it's beautifully formatted and linted JavaScript, almost as if Douglas Crockford wrote it himself.

### 1.3 Why and when to use TypeScript ###

// todo

## 2. Get TypeScript ##

The most interesting thing about the TypeScript compiler (or *tsc* for short) is the fact it's all written in TypeScript.  The TypeScript compiler is [open sourced on CodePlex](http://typescript.codeplex.com/). 

The second interesting thing about the TypeScript compiler is that it's a Node.js app.  So to install tsc, you'll first need to install Node.js and then use Node's package manager (npm) to actually install the Typescript compiler.

1) Install Node.js (if you don't have it)  
[Download and Install Node.js Here](http://nodejs.org/download/)

2) Install TypeScript (tsc) via npm, via the terminal  
`npm install -g typescript`

You're done!  

You should now have `tsc` available in the terminal.

## The Type in TypeScript ##

TypeScript gives you the ability to strictly type your JavaScript code. Defining types in TypeScript is fairly pleasant and not overerly verbose.  The majority of the typing occurs at the API level with inner variable types inferred by default.

## TypeScript Syntax ##

### Types ###

TypeScript comes with a few builtin types.  You'll notice nothing new here coming from a JavaScript world.

- string
- number
- bool
- any
- void

To have a typed array you simply add square brackets

- string[]
- number[]
- bool[]
- any[]
- void[]

To type a variable you use the familiar colon notation.

`var foo:number;`

The same applies to function arguments 

`function add5(base:number) {
  return base + 5;
}`

Notice that I didn't type *add5*, the compiler was able to infer that *add5* returns a number.

## Arrow Functions ##

A future EcmaScript spec declares a short hand notation for functions.  You may never write the word *function* ever again ;).

This list of punctuation is it .. In fact, this is a completely valid function ..

    ()=>{};

OK, Let's break it down... `()=>` is short for `function()` and `{}` is the function body.  Let's look at a futher example ..

    (arg1:number, arg2:number)=>{return arg1+arg2}

This will be converted into ..

	// JavaScript
    function (arg1, arg2) {
        return arg1 + arg2;
    }

Simple stuff, right?  But wait, there's more!  

If we want a truly shorthand function, we shouldn't have to include a `return` statement and those curly braces don't seem to add anything in this simple example.  Let's try it again ..

    (arg1:number, arg2:number)=>arg1+arg2

Great, that's much cleaner and the output is exactly the same.

One more thing!  Async. callbacks are one of the most common use-cases for anonymous functions. But when dealing with an async callback, we are likely to lose the correct `this` context. This leads to developers using `var that = this;` to keep a local copy of context state.  The Arrow Function (in TypeScript) tries to brige this awkward gap by performing this hack for you.  

In the following example we've created a function called delaySetter.  This function will wait one second using async setTimeout before updating foo on this. 

    var delaySetter = (val:number)=> setTimeout(()=> this.foo = val, 1000);

The TypeScript compiler will produce the following JavaScript.  Notice the `var _this = this;`.

	// JavaScript
    var delaySetter = function (val) {
        var _this = this;
        return setTimeout(function () {
            return _this.foo = val;
        }, 1000);
    };

You'll loose some of the shorthand features once you require multiple statements in your function.  You'll once again need an explicit return and curly braces ...

    var aPlusB = ()=> {
    	var a = 123;
    	var b = 321;
    	return a+b;
    }

### Optional Function Arguments ###

Something missing from JavaScript functions are default argument values or optional arguments.  TypeScript makes it easy and familiar.

	var logger = (msg:string="empty message")=> console.log(msg)

	logger(); // "empty message"
	logger("hello world"); // "hello world"

For reference, here's generated JS code from `tsc`.

	var logger = function (msg) {
	    if (typeof msg === "undefined") { msg = "empty message"; }
	    return console.log(msg);
	};
	logger();
	logger("hello world");

Note that required arguments can not follow optional arguments.

	(msg:string="empty message", foo:string)=> console.log(msg, foo) // BAD :(

	(foo:string, msg:string="empty message")=> console.log(msg, foo) // GOOD :)



### Interfaces ###

Interfaces are the most fundamental concept in TypeScript. They can describe objects and functions, and like in most standard OOP languages, an interface is a type and can be implemented by a class and extended. But unlike traditional OOP, they are in-fact enablers of TypeScript's super cool structural type system.  You see, an interface in TypeScript is simply compile time duck typing. 

Here's a classic duck typing example.  In duck typing we say that an object is a Duck if it sounds like and looks like a duck; if it can quack and has feathers.  Unlike a lot of common duck typing, in TypeScript we can explicitly declare an interface for a Duck.  If an object implements the interface of Duck, then it must be a Duck.  If an object is passed to `inTheForest` that fails to fulfill the requirements of the Duck interface, then the compiler will give an error.

	interface Duck {
		quack:()=>void;
		feathers:()=>void;
	}

	var person = {
		quack:()=> console.log("The person imitates a duck."),
		feathers:()=> console.log("The person takes a feather from the ground and shows it.")
	}

	var duck = {
		quack:()=> console.log("Quaaaaaack!"),
		feathers:()=> console.log("The duck has white and gray feathers.")
	}


	var bird = {
		tweet:()=> console.log("Tweet. Tweet."),
		feathers:()=> console.log("The bird has white and blue feathers.")
	}

	var inTheForest = (duck:Duck)=> {
		duck.quack()
		duck.feathers()
	}

	inTheForest(person);
	inTheForest(duck);

	inTheForest(bird); // FAIL!!



Here's the unaltered compiled JavaScript code from `tsc` for reference. 

	// JavaScript
	var person = {
	    quack: function () {
	        return console.log("The person imitates a duck.");
	    },
	    feathers: function () {
	        return console.log("The person takes a feather from the ground and shows it.");
	    }
	};
	var duck = {
	    quack: function () {
	        return console.log("Quaaaaaack!");
	    },
	    feathers: function () {
	        return console.log("The duck has white and gray feathers.");
	    }
	};
	var bird = {
	    tweet: function () {
	        return console.log("Tweet. Tweet.");
	    },
	    feathers: function () {
	        return console.log("The bird has white and blue feathers.");
	    }
	};
	var inTheForest = function (duck) {
	    duck.quack();
	    duck.feathers();
	};
	inTheForest(person);
	inTheForest(duck);
	inTheForest(bird); 


This example shows how we can nest interface types within one another; treating them as actual data types.  


	interface Point {
		x: number;
		y: number;
	}

	interface Rectangle {
		topLeft: Point;
		bottomRight: Point;
	}

	var p1:Point = {x:10, y:20}
	var p2:Point = {x:60, y:40}

	var getWidth = (rect:Rectangle)=> rect.bottomRight.x - rect.topLeft.x;
	var getHeight = (rect:Rectangle)=> rect.bottomRight.y - rect.topLeft.y;

	var getDepth = (rect:Rectangle)=> rect.bottomRight.z - rect.topLeft.z; // Compile FAIL.  Point has no z.

	console.log( getWidth({topLeft:p1, bottomRight:p2}) )


Since JS functions are also objects, TypeScript offers a way to type those too.

	interface MyCallback {
 		(arg1:string): bool;
	}

	var asyncFunc = (callback:MyCallback)=> {
		var result = callback("hello");
		if (result) {
			console.log("world")
		}
	}

	asyncFunc( (arg:string)=> arg == "hello" )

We'll talk more about interfaces in below in the "Working with existing JavaScript libs" section.



### Classes ###


Classes, the fundamental building block in OOP.  JavaScript's Prototypical inheritance model has always been met with a little confusion.  A future EcmaScript spec defines a much more structured classical approach that TypeScript has also adopted.

Here's an example of a simple Class in TypeScript.  Notice the `constructor` method,  this is reserved part of the TypeScript Class spec, and just like any other constructor, gets called upon instantiation.  Also notice the public `greeting` property.  You'll also start to see the lack of commas separating methods.

	class Greeter {
		greeting: string;
		constructor(message: string) {
			this.greeting = message;
		}
		greet() {
			return "Hello, " + this.greeting;
		}
	}   

And just as in regular JavaScript, we can use `new` to create an instance of the Class.

	var greeter = new Greeter("world");

Here's the compiled JavaScript for reference ..

	var Greeter = (function () {
	    function Greeter(message) {
	        this.greeting = message;
	    }
	    Greeter.prototype.greet = function () {
	        return "Hello, " + this.greeting;
	    };
	    return Greeter;
	})();
	var greeter = new Greeter("world");


Let's reimplement our Point interface from earlier as a Class.

	class Point {
		x:number;
		y:number;
		constructor(x:number=0, y:number=0) {
			this.x = x;
			this.y = y;
		}
	}

	var p1 = new Point();

	console.log(p1.x,p1.y);

Again, here's the generated JavaScript ..

	var Point = (function () {
	    function Point(x, y) {
	        if (typeof x === "undefined") { x = 0; }
	        if (typeof y === "undefined") { y = 0; }
	        this.x = x;
	        this.y = y;
	    }
	    return Point;
	})();
	var p1 = new Point();
	console.log(p1.x, p1.y);

We can go a spec further in cleaning up this Point example.  In our constructor we're simply mapping the constructor arguments to property members of the same name; it seems a little redundant. And in a way, we're listing the x,y properties twice.  We need some shorthand!  Fortunately, TypeSciprt gives us some shorthand to implicitly setup public properties from constructor arguments.  Notice the public keyword in the example below.

    class Point {
		constructor(public x:number=0, public y:number=0) {}
	}

	var p1 = new Point();

	console.log(p1.x,p1.y);

The output JS from this code stays exactly the same as above.  You'll have noticed the use of optional arguments in this example,  these are *not* required in order to use this constructor shorthand.

One final last thing about Classes; `static` members and methods.  A `static` is bound to the Class and not to the instance of the Class. Static methods usually offer some sort of utility functionality. We'll use the Point class again to highlight statics.

    class Point {
		
		static origin = new Point();

    	static distance(p1:Point, p2:Point=Point.origin) {
		    var x = p2.x - p1.x;
		    var y = p2.y - p1.y;
			return Math.sqrt(x*x + y*y);
		}
		
		static nearest(p:Point, points:Point[]) {
		    var nearestPoint = points[0];
			var nearestDistance = Point.distance(p,nearestPoint);
			var i:number;
			var distance:number;
			for (i=1; i<points.length; i++) {
				distance = Point.distance(p, points[i]);
				if (distance < nearestDistance) {
					nearestDistance = distance;
					nearestPoint = points[i];
				}
			}
			return nearestPoint;
		}

		constructor(public x:number=0, public y:number=0) {}
		
	}
	
	Point.nearest(new Point(), [new Point(23,-21),new Point(300,4), new Point(13,32)]);

Again, here's the generated JavaScript

	var Point = (function () {
	    function Point(x, y) {
	        if (typeof x === "undefined") { x = 0; }
	        if (typeof y === "undefined") { y = 0; }
	        this.x = x;
	        this.y = y;
	    }
	    Point.origin = new Point();
	    Point.distance = function distance(p1, p2) {
	        if (typeof p2 === "undefined") { p2 = Point.origin; }
	        var x = p2.x - p1.x;
	        var y = p2.y - p1.y;
	        return Math.sqrt(x * x + y * y);
	    }
	    Point.nearest = function nearest(p, points) {
	        var nearestPoint = points[0];
	        var nearestDistance = Point.distance(p, nearestPoint);
	        var i;
	        var distance;
	        for(i = 1; i < points.length; i++) {
	            distance = Point.distance(p, points[i]);
	            if(distance < nearestDistance) {
	                nearestDistance = distance;
	                nearestPoint = points[i];
	            }
	        }
	        return nearestPoint;
	    }
	    return Point;
	})();
	Point.nearest(new Point(), [
	    new Point(23, -21), 
	    new Point(300, 4), 
	    new Point(13, 32)
	]);

Finally, let's refactor our Rectangle interface into a Class

	
	class Rectangle {
		constructor(public topLeft:Point=Point.origin, public bottomRight:Point=Point.origin) {}
		getWidth() {
			return this.bottomRight.x - this.topLeft.x;
		}
		getHeight() {
			return this.bottomRight.y - this.topLeft.y;
		}
	}

This produces ..

	var Rectangle = (function () {
	    function Rectangle(topLeft, bottomRight) {
	        if (typeof topLeft === "undefined") { topLeft = Point.origin; }
	        if (typeof bottomRight === "undefined") { bottomRight = Point.origin; }
	        this.topLeft = topLeft;
	        this.bottomRight = bottomRight;
	    }
	    Rectangle.prototype.getWidth = function () {
	        return this.bottomRight.x - this.topLeft.x;
	    };
	    Rectangle.prototype.getHeight = function () {
	        return this.bottomRight.y - this.topLeft.y;
	    };
	    return Rectangle;
	})();


Something that's worth pointing out about Classes is how they differ from interfaces.  With interfaces, can perform compile time checked duck typing.  Classes do not offer the same flexibility.  Below we've created two Classes (Point,Dot) with the same interface (x,y).  When we create a distance method that takes two Points and try to use a Dot the compiler will give an error.

    class Point {
		constructor(public x:number=0, public y:number=0) {}
	}
	
	class Dot {
		constructor(public x:number=0, public y:number=0) {}
	}
	
	var distance = (p1:Point, p2:Point)=> {
		var x = p2.x - p1.x;
	    var y = p2.y - p1.y;
	    return Math.sqrt(x * x + y * y);
	}
	
	distance(new Dot(), new Point()); // ERROR!


This could be overcome using an interface (note: using the `I` prefix for interfaces upsets me but lets roll with it).

	interface IPoint {
		x:number;
		y:number;
	}

    class Point  {
		constructor(public x:number=0, public y:number=0) {}
	}
	
	class Dot {
		constructor(public x:number=0, public y:number=0) {}
	}
	
	var distance = (p1:IPoint, p2:IPoint)=> {
		var x = p2.x - p1.x;
	    var y = p2.y - p1.y;
	    return Math.sqrt(x * x + y * y);
	}
	
	distance(new Dot(), new Point()); // SUCCESS!


A more correct way of saying a Class implements and interface is to use the `implements` keyword.  This ensure that our Classes are built correctly before we start using them.


	interface IPoint {
		x:number;
		y:number;
	}

    class Point implements IPoint {
		constructor(public x:number=0, public y:number=0) {}
	}
	
	class Dot implements IPoint {
		constructor(public x:number=0, public y:number=0) {}
	}

	// ERROR, we didn't implement x
	class Cord implements IPoint {
		constructor(public x:number=0, public z:number=0) {}
	}
	



### Inheritance ###

You can't have Classes without Inheritance and TypeScript is no exception.  TypeScript also has the ability to extend interfaces, let's look at that first ..

	interface Point {
		x:number;
		y:number;
	}

	interface Point3D extends Point {
		z:number
	}


Now, let's refactor this as Classes

	class Point {
		constructor(public x:number=0, public y:number=0) {}
	}

	class Point3D extends Point {
		constructor(public z:number=0) { super() }
	}

Here's the generated JavaScript from `tsc`.  Notice the `__extends` glue, this is how the inheritance is performed back in JavaScript land.

	var __extends = this.__extends || function (d, b) {
	    function __() { this.constructor = d; }
	    __.prototype = b.prototype;
	    d.prototype = new __();
	};
	var Point = (function () {
	    function Point(x, y) {
	        if (typeof x === "undefined") { x = 0; }
	        if (typeof y === "undefined") { y = 0; }
	        this.x = x;
	        this.y = y;
	    }
	    return Point;
	})();
	var Point3D = (function (_super) {
	    __extends(Point3D, _super);
	    function Point3D(z) {
	        if (typeof z === "undefined") { z = 0; }
	        _super.call(this);
	        this.z = z;
	    }
	    return Point3D;
	})(Point);



### Module Pattern ###

The modules pattern is a popular JavaScript coding pattern for encapsulating something like a small library.  It takes advantage of function closures to hide implementation and "export" a public API.  For people who've never seen the JavaScript module pattern before, here's one variant..

	var Module = (function() {
		
		var counter = 0;

		return {
			increment: function() {
				counter++;
			},
			getCount: function() {
				return counter;
			}
		}

	}());

	Module.increment();
	console.log(Module.getCount); // 1

The module pattern is fun but it's not very pretty to look at. TypeScript tries to solve this by delivering a clear structured syntax.

	module Module {
		var counter = 0;
	    export function increment() {
			counter++;
		}
		export function getCount()=> counter
	}

The actually JavaScript module pattern variant `tsc` generates is a little different from the first example but it achieves the same result.

	var Module;
	(function (Module) {
	    var counter = 0;
	    function increment() {
	        counter++;
	    }
	    Module.increment = increment;
	    function getCount() {
	        return counter;
	    }
	    Module.getCount = getCount;
	})(Module || (Module = {}));

Here's another example of a TypeScript module that exports a Class instead of a function.  You can see that Modules and a simple way of name-spacing your libs.

	module Sayings {
	    export class Greeter {
	        greeting: string;
	        constructor (message: string) {
	            this.greeting = message;
	        }
	        greet() {
	            return "Hello, " + this.greeting;
	        }
	    }
	}
	var greeter = new Sayings.Greeter("world");



### Common.js & AMD Modules ###

TypeScript introduces a new way of importing external dependancies.

    import MyLib = module("./MyLib");

With the AMD compiler flag enabled, this will produce ..

    define(["require", "exports", "./MyLib"], function(require, exports, MyLib) { . . code . . });


// todo


## Working with existing JavaScript libs ##

// todo













