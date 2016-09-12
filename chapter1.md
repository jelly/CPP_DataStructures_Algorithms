C++ Classes
============

Basic class syntax
-------------------

```
Class Foo
{
	public:
		Foo() {
			 bar = 0;
		}

		Foo(int val) {
			bar = val;
		}

		int read() {
			return bar;
		}

		void write(int x) {
			bar = x;
		}

	private:
		int bar;
}
```

*Initializer list*, the initializer list is used to initialize data members directly. What happens is as following, Copy constructor of Type class is called to initialize : bar(val). The arguments for the initializer list are used to copy construct "bar" directly. Then the deconstructor for "val" is called since "val" goes out of scope. A normal constructor does the following, constructor for "val" is called, the assignment operator of "Type" is called inside the bdoy of Foo(int val) constructor to assign and then the destructor of "Type" is called since "val" goes out of scope.

```
class Foo
{
	public:
		explicit Foo(int val = 0) : bar(val) { }
	private:
		int bar;
}

**Explicit constructor** is used to prevent type conversion happening in the following example.

```
Foo obj;
obj = 34.0; // should not compile type mismatch, no match for operator= 
```

Without **explicit**, C++ uses implicit type conversion, in which a temporary object is created that makes an assigment (or parameter to a function) compatible.

```
IntCell temp = 37;
obj = temp;
```

**Constant member function** a constant member function is a fixed which does not mutate the state of the class for example the Foo::read() function above.

Preprocessor commands
---------------------

A header file such as .h, usually they have header guards to prevent duplicate inclusio, which might be illegal for example:

```
#ifndef CLASS_H
#define CLASS_H
#endif
```

Scoping operator
----------------


