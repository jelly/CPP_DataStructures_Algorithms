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

**Initializer list**, the initializer list is used to initialize data members directly. What happens is as following, Copy constructor of Type class is called to initialize : bar(val). The arguments for the initializer list are used to copy construct "bar" directly. Then the deconstructor for "val" is called since "val" goes out of scope. A normal constructor does the following, constructor for "val" is called, the assignment operator of "Type" is called inside the bdoy of Foo(int val) constructor to assign and then the destructor of "Type" is called since "val" goes out of scope.

```
class Foo
{
	public:
		explicit Foo(int val = 0) : bar(val) { }
	private:
		int bar;
}
```

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

:: is called the scoping operator, since otherwise the global scope would be literred with a lot of functions. The syntax is ClassName::member.

C++ Pointers
-------------

A variable that sotres the address where another object resides in memory.

Foo *obj, the * indicates that obj is a pointer value of obj is the address of the object that it points at.

The address of operator & returns the memory location where an object resides.

Two pointers are equal if the point to the same object. If a pointer variable points to a class type, then a (visible) member of the object being pointed at can be accessed via the -> operator.

C++ allows pointers to be compared with <, for pointers lhs and rhs, lhs < rhs is true if the object pointed at by lhs is stored at a lower memory location than the object pointed at by rhs.

Parameter passing
-----------------

C passes all arguments using call by value, the acual argument is copied into the formal parameter.
In C++ copying large objects is sometimes rather inefficient, additionally it can be desirable to change the passed value. C++ therefore has three ways to pass arguments:

```
double avg( const vector<int> & arr, int n, bool & errorFlag);
```

* call by constant reference, if the value of the argument cannot be changed by the formal parameter If the type is a primive type is call by value (with const) if it's a type is a class type call by constant reference can be used.
* If the formal parameter should be able to change the value of the actual argument, then you must use call by reference.

Return passing
--------------

When returning a reference, be careful not te return a local variable as the following example does:

```
const string & findMax(const vector<string> & arr)
{
	string maxvalue = arr[0]

	for (int i = 1; i < arr.size(); i++) {
		if (maxvalue < arr[i] )
			maxvalue = arr[i];
	}

	return maxvalue;
}
```

g++ luckily throws a compiler warning, -Wreturn-local-addr. Since the local variable is out of scope and already removed, calling and assinging a variable from this function leads to a coredump.

Destructor
----------

The destructor is called when an object goes out of scope or is subjected to a delete. Typically used to clean up allocated resources.


```
Foo::~Foo() {
}
```
Copy Constructor
----------------

Special constructor that is required to construct a new object, initialized to a copy of the same type of object.

```
Foo B = C;
Foo B(C);
```

Operator=
----------

Copy assignment operator is called when = is applied to two objects. After they both have been previously constructed.


```
Foo B;
Foo C;
B = C;
```

Templates
---------

Function templates
------------------

A **function template** is not an actual function, but a pattern for what could become a function.

```
template <typename Cmp>
const Cmp & findMax(const std::vector<Cmp> & v)
{
	int max = 0;

	for (int i = 0; i < v.size(); i++)
		if (v[max] < v[i])
			max = i;
	return v[max];
}

int main()
{
	std::vector<int> v1(37, 40);
	std::vector<double> v2(42.0, 90.0);

	std::cout << findMax(v1) << std::endl;
	std::cout << findMax(v2) << std::endl;

	return 0;
}
```

Class templates
---------------

A class template is much the same as a function template.

```
template <typename Obj>
class Cell
{
	public:
		explicit Cell(const Obj & input = Obj()) : val(input) {}
		const Obj & read( ) const
		{ return val; }
		void write(const Obj & obj)
		{ val = obj; }
	private:
		Obj val;
}

int main()
{
	Cell<int> c1;
	Cell<std::string> c2;

	c1.write(38);
	std::cout << c1.read() << std::endl;

	c2.write("hello world!");
	std::cout << c2.read() << std::endl;
	
	return 0;
}

Operator overloading
--------------------

In C++ it's possible to overload the default behaviour of for example the << or the < operator for a class.

```
class Cell
{
	public:
		explicit Cell(const int & input) : val(input) {}
		const int & read( ) const
		{ return val; }
		void write(const int & v)
		{ val = v; }
		bool operator< (const Cell & rhs) const
		{ return val < rhs.val; }
	private:
		int val;
};

int main()
{
	Cell c1(1);
	Cell c2(3);

	if (c1 < c2)
		std::cout << "c1 < c2" << std::endl;
	
	return 0;
}
```

Function objects
----------------

The findMax function template expects a class to implement the operator<, if a class does not implement this operator or if we wish to use our custom comparison function we can use a function object. The function object is a class instance which defines the comparison function.

```
template <typename Obj, typename Cmp>
const Obj & findMax(const std::vector<Obj> & v, Cmp cmp)
{
	int max = 0;

	for (int i = 0; i < v.size(); i++)
		if (cmp.isSmaller(v[max], v[i]))
			max = i;
	return v[max];
}

class CaseInsentiveCompare
{
	public:
		bool isSmaller(const std::string & lhs, const std::string & rhs) const
		{ return strcasecmp(lhs.c_str(), rhs.c_str()) < 0; }
};

int main()
{
	std::vector<std::string> v1(3);
	v1[0] = "Cat"; v1[1] = "Dog"; v1[2] = "Nyancat";

	std::cout << findMax(v1, CaseInsentiveCompare()) << std::endl;

	return 0;
}
```
