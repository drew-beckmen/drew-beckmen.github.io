---
layout: post
title: 'Duck Typing in Ruby: Object-Oriented Design'
published: true
---

# Demystifying Duck Typing in Ruby: Take Your OO Design Skills to the Next Level

> "If an object quacks like a duck and walks like a duck, then it's a duck"

> "Unlike static and dynamic typing of variables, duck typing is not a language feature, but a design principle which is a combination of dynamic typing and object-oriented programming" —Matz

One of the central tenets of Object-Oriented design is to write code that is modular, reusable, and open to change. In order to build code that does not resist change, programmers must evaluate and reduce dependencies within classes. Since Ruby is a dynamically typed programming language, **duck typing** can be used to decouple dependencies and increase the flexibility of the code. 

Before getting into the details of duck typing in Ruby, we need to clarify the distinction between statically and dynamically typed languages. Java, like Ruby, is an object-oriented language. However, unlike Ruby, Java is statically typed. 

We can define some simple Java code as follows:
```java 
class Main {
	public static void main(String[] args) {
		int x = 5; 
		x = "Hello"; 
		System.out.println(x); 
	}
}
```

When the Java compiler looks at that code, it will be very unhappy. Why? Because Java is statically typed. On line 4, we try to reassign the variable `x`, which we initially defined as an `int`, to a value of type String. As a result, we will get an error: 

```
Main.java:4: error: incompatible types: String cannot be converted to int
    x = "Hello"; 
```

The analogous code in Ruby would be: 

```ruby 
x = 5
x = "Hello"
puts x #=> "Hello"
```

When this code is interpreted, Ruby will not throw an error. The variable x is not bound to a specific type. Although x is initially an Integer object, we can change it to a String object with no problems at all! There is no implicit type checking in Ruby when the code is interpreted at runtime. 

We can insert a few more lines of code to clarify what is going on under the hood: 
```ruby 
x = 5
puts x.class #=> Integer 
x = "Hello"
puts x.class #=> String 
```

Our variable `x` is not restricted to one specific type as it is in Java. As a fun aside, what would the following code output? 

```ruby 
class Animal 
end 

x = 5 

puts Animal.class  # What would print in the console?
puts Animal.superclass #=> Object
```
The code will print out Class. What does this mean? It solidifies the notion that, in Ruby, everything is an object. Even Animal, which is a class itself, is an instance of the class Class! That may seem crazy, but it's true! Another important note is that any class which does not explicitly define a parent class via inheritance will inherit from Ruby's built-in Object class. Hence, `puts Animal.superclass` will print Object on the console.

Now, back to the main subject at hand...

So what is duck typing? Unlike concrete topics in OO design such as inheritance, duck typing is more of a philosophy than a concept rooted in Ruby syntax. The fundamental idea of duck typing is that Ruby does not care about an object's class; Ruby's sole concern is whether a given object can respond to the appropriate message (ie. method). In other words, Rubyists should shift away from a class-based perspective and adopt a message-based perspective. An object is defined by what it can do and what messages it can respond to (its interface), not what it is (the class).

To illustrate the concept, I will provide a simple example. First, we will look at an example in which a programmer ignores Ruby's dynamically typed nature and assumes a "static type" approach. 

Since we are working with object-oriented design, we will create an example that models real-world phenomena. Our example will deal with a `Trip` class. The `Trip` class will have an instance method called `prepare`, which will handle all the actions of preparing for a specific trip. 

```ruby 
class Trip 
	def prepare(preparers)
		preparers.each do |preparer|
			case preparer 
			when TravelAgent
				preparer.book_flights
			when Vacationer
				preparer.pack_bags
			end
		end 
	end 
end 

class TravelAgent
	def book_flights 
		puts "Flights are booked!"
	end 
end 

class Vacationer
	def pack_bags 
		puts "Bags are packed!"
	end 
end 
```
In this code, I have taken a class-focused approach. Using a case statement, I check the class of an individual preparer before calling an instance method within that preparer's class. However, this approach is short-sighted. It introduces a significant number of dependencies into your code, and as the preparation for a trip becomes more extensive, those dependencies will only increase. Our code depends on the existence of classes such as Vacationer and TravelAgent. 

What if we shift our frame of thinking? Since Ruby is dynamically typed, an object is not limited by its type, but by the messages to which it can respond. If we use case statements to check the class of an object, we are essentially writing code as if Ruby were statically typed. We need to rework the code to harness the power of dynamically typed languages. 

How could we design our code so that we have just a single dependency? Answer: duck typing.

Our single dependency will be that every object in the preparers array is a `Preparer`, which will be our duck type. `Preparer` is not a class we will code or an abstract class we will define as in Java. Instead, `Preparer` is a duck type in which every `Preparer` instance will respond to a single method: prepare_trip. 

First, we will define our `Trip` class as follows:
```ruby 
class Trip 
	def prepare(preparers)
		preparers.each do |preparer|
			preparer.prepare_trip
		end 
	end 
end 
```

As you can already see, the number of dependencies has dropped dramatically. Now, the prepare method only knows that each preparer responds to a method `prepare_trip`. 

Now, let's rework our TravelAgent and Vacationer classes!
```ruby 
class TravelAgent
	def prepare_trip  
		puts "Flights are booked!"
	end 
end 

class Vacationer
	def prepare_trip 
		puts "Bags are packed!"
	end 
end 
```

To test the code, we can run the following line: 
```ruby 
Trip.new.prepare([TravelAgent.new, Vacationer.new])
```

We will get the following output:
```
Flights are booked!
Bags are packed!
```

Magic! Now, we don't care whether a preparer is an instance of TravelAgent or Vacationer. The class of preparer is not relevant — we only need to ensure that each preparer responds to the message prepare_trip. By creating a Preparer duck, we harness the power of Ruby as a dynamically typed language. 

To reinforce the benefits of duck typing and dynamically typed languages in general, I have included the analogous Java code in the repl below. 

<iframe height="400px" width="100%" src="https://repl.it/@drewbeckmen/NoDuckTypingInJava?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Press the green play button to see the output. The program will print the same output as the Ruby program, but the Java implementation is drastically different. Rather than creating a duck type, Java requires us to explicitly state that our Vacationer and TravelAgent classes extend the abstract class Preparer. We can then define an array of Preparer objects in our main method, which is then passed to the prepare method within our Trip class. 

In Ruby, there is no need for abstract classes and type checking. All you need to focus on are the messages to which your objects respond!

For many people accustomed to statically typed languages, the concept of duck typing may seem foreign. However, if you are able to get beyond the "class-first" mindset and embrace objects for their behaviors rather than their types, you will be well on your way to becoming a powerful Ruby programmer.
