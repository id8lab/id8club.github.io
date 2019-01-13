---
layout: default
title: C++ Workshop 5 - Templates
description: "C++ Templates"
permalink: cplusplusworkshop5.html
---

# C++ Workshop 5 - Templates

C++ Templates
=============

Consider the situation where a piece of code needs to be reused multiple times, in order to accommodate a variety of data types, and contains logic that is the same regardless of the data type in use:

```cpp
//Version for ints
int add(int a, int b) {
	return a + b;
}

//Version for doubles
double add(double a, double b) {
	return a + b;
}

//Version for chars
char add(char a, char b) {
	return a + b;
}

int main()
{
	add(0, 1);     //Use the version for ints
	add(0.0, 0.1); //Use the version for doubles
	add('a', 'b'); //Use the version for chars
	
	return 0;
}
```

Manually writing different versions of the code by copy-pasting it and replacing the types is tedious, and cannot accommodate arbitrary types that have not been explicitly considered. To solve this problem, C++ includes a language feature known as **templates**.

Templates allow the programmer to specify type-agnostic code, known as a **template**. A template is specified only once. The compiler then performs the copy-pasting and type substitution automatically. For example, the code from the listing above can be rewritten using a template like so:

```cpp
//Single template version to work for all types
//All places where T appears will be replaced with the correct type when the template is filled out by the compiler
template <typename T>
T add(T a, T b) {
	return a + b;
}

int main()
{
	add(0, 1);     //Use the version for ints
	add(0.0, 0.1); //Use the version for doubles
	add('a', 'b'); //Use the version for chars
	
	return 0;
}
```

When the compiler sees a call to the template function `add()`, it uses a process known as **type deduction** to determine the data types of the arguments that have been supplied. The template code is then duplicated, and the template parameter **`T`** is replaced with the correct type. The end result is the same is if the three versions of the function had been written manually, but with two important benefits:

- The amount of redundant code is reduced.
- The compiler can fill out the template for **any type** we request, not only the types the original author of the function explicitly included support for.

Of course, there are practical limitations to the types that can be used with this particular template function. Although the compiler will generate versions of the code for any type that we desire, a compiler error will occur if we attempt to utilise a type that does not support the addition operator (`+`). Naturally, this would also be the case if we were to manually copy-paste the code and replace the types with a data type that does not support the addition operator (`+`) - the statement `return a + b` is invalid in such a situation.


Template specialisation
=======================

When examining the behaviour of templates using the example code from the previous section, it becomes apparent that templates only work for types that can be treated in an identical manner. However, there may be instances when the programmer wishes to add support for a type that must be treated differently to the general case. C++ accommodates such situations through a feature known as **template specialisation**.

Template specialisation allows the programmer to explicitly provide a different version of the template code for handling a specific type. Consider the class below:

```cpp
class MyClass
{
	public:
	
		MyClass addInstances(const MyClass& other) const;
		
		//Other class details go here...
};
```

Attempting to call the template function `add`, from the examples in the previous section, with instances of the `MyClass` class would result in a compiler error, because the addition operator (`+`) has not been overloaded for this class. If we are unable to modify the `MyClass` class for any reason, then the only alternative is to provide a template specialisation of the `add()` function to handle `MyClass` instances specially:

```cpp
template <>
MyClass add(MyClass a, MyClass b) {
	return a.addInstances(b);
}
```

The typical syntax for a template specialisation is to remove the template parameter being specialised (in this case, `typename T`) from the `template` declaration, and replace it with the specific type we are specialising for. However, there are cases when this is not the case, particularly when specialising for pointers.


Specialisation for pointers and references
------------------------------------------

When we are specialising for a pointer or reference to a type, we actually leave the template parameter unmodified. In the case of the `add()` template function from the previous examples, we would leave `typename T` unmodified in the `template` declaration, and replace `T` with `T*` or `T&` in the rest of the template code:

```cpp
//Template specialisation for pointers
template <typename T>
T add(T* a, T* b) {
	return *a + *b;
}

//Template specialisation for references
//Note that this specialisation is REDUNDANT, as the body code is the same as the original template.
template <typename T>
T add(T& a, T& b) {
	return a + b;
}
```

Of course, a specialisation for a non-const reference is completely redundant, as the body code is exactly the same. Likewise, a specialisation for a pointer to `const T` would be redundant in the example above, as it would have the same body code as the version for the pointer to `T` (which will match a pointer to `const T` anyway.)

The only time it is necessary to provide a template specialisation is **when the body code is different** in some way. For primitive types, a pointer specialisation such as the one above will often suffice. When dealing with instances of classes (particularly those from the C++ standard library), different code may be required for treatment `const` references or pointers.


Multiple template parameters
============================

When declaring a template, we can specify any number of template parameters. **All** of these parameters need to be substituted with real values when the template is filled out by the compiler.

As an example, consider the following function that has different versions for different types:

```cpp
int addDifferentTypes(int a, double b) {
	return a + (int)b;
}

char addDifferentTypes(char a, int b) {
	return a + (char)b;
}

double addDifferentTypes(double a, int b) {
	return a + (double)b;
}

int main()
{
	addDifferentTypes(0, 0.1);  //Uses the version for int and double
	addDifferentTypes('a', 1);  //Uses the version for char and int
	addDifferentTypes(1.0, 1);  //Uses the version for double and int
	
	return 0;
}
```

Manually writing different versions of the function for every possible combination of types would be very tedious indeed, making the template version particularly convenient:

```cpp
template <typename T, typename K>
T addDifferentTypes(T a, K b) {
	return a + (T)b;
}

int main()
{
	addDifferentTypes(0, 0.1);  //Uses the version for int and double
	addDifferentTypes('a', 1);  //Uses the version for char and int
	addDifferentTypes(1.0, 1);  //Uses the version for double and int
	
	return 0;
}
```

Note that **the names of the template parameters are arbitrary**, and any names can be utilised, so long as they **are unique** within the template, and they follow the normal identifier naming rules:

- Must start with a letter or an underscore
- Can contain letters, numbers, and underscores
- Cannot be an existing language keyword


Partial template specialisation
===============================

Templates that have multiple template parameters can be *partially specialised* - that is, we can provide a specialisation that provides a value for some, but not all, of the template parameters.

For example, say we wished to specialise the `addDifferentTypes()` template function to behave differently if the first argument is an `int`:

```cpp
//Multiplies "a" by 100 when "a" is an int
template <typename K>
int addDifferentTypes(int a, K b) {
	return (a * 100) + (int)b;
}
```

Or if we wanted to behave differently when the *second* argument is an `int` instead:

```cpp
//Multiplies "b" by 100 when "b" is an int
template <typename T>
T addDifferentTypes(T a, int b) {
	return a + (T)(b * 100);
}
```

Explicitly specifying template parameter values
===============================================

Although argument type deduction works very well for a function's arguments, **the compiler cannot deduce a function's return type** in instances when it does not share a template parameter with one of the function's arguments. (In the previous example, the return type of the `addDifferentTypes()` function was the same as the first argument, which was already deduced by the compiler.)

In cases when the return type is specified by a different template parameter to the argument types, it must be **explicitly specified by the programmer** when calling the template function.

Consider the following code:

```cpp
template <typename T, typename K, typename R>
R addDifferentTypes(T a, K b) {
	return (R)a + (R)b;
}
```

This version of the `addDifferentTypes()` function can return the result as any type the programmer wishes (the previous version just used the type of the first function argument.) To call this template function, we need to explicitly specify the values for the template parameters:

```cpp
int main()
{
	addDifferentTypes<int, double, double>(0, 0.1);  //Uses the version for int and double, returning a double
	addDifferentTypes<char, int, char>('a', 1);      //Uses the version for char and int, returning a char
	addDifferentTypes<double, int, int>(1.0, 1);     //Uses the version for double and int, returning an int
	
	return 0;
}
```

It is important to note that **template parameter values can be omitted right-to-left**, relying on argument type deduction to provide the correct values for the omitted parameters. Unfortunately, because the parameter for the return type has been specified as the rightmost parameter, it is necessary to provide values for all parameters to the left of it, losing the benefit of type deduction. To remedy this, it is useful to **specify the return type parameter as the first template parameter**. This way, only the value for the return type needs to be explicitly specified, and argument type deduction can provide the values for the remaining parameters:

```cpp
template <typename R, typename T, typename K>
R addDifferentTypes(T a, K b) {
	return (R)a + (R)b;
}

int main()
{
	addDifferentTypes<double>(0, 0.1);  //Uses the version for int and double, returning a double
	addDifferentTypes<char>('a', 1);    //Uses the version for char and int, returning a char
	addDifferentTypes<int>(1.0, 1);     //Uses the version for double and int, returning an int
	
	return 0;
}
```


Non-type template parameters
============================

In addition to substituting types, C++ templates also support template parameters that represent other data. For example, consider the template function in the code example below, which includes an `int` template parameter:

```cpp
template <typename T, int NumIterations>
T processValue(T value)
{
	T result = value;
	
	for (int i = 1; i <= NumIterations; ++i) {
		result += (value / i);
	}
	
	return result;
}
```

Of course, in the example above, the template parameter could simply be a function argument. However, the use of an iteration count that is known at compile time provides the compiler with the ability to more aggressively optimise the `for` loop, and may even result in the loop being unrolled completely at lower iteration counts. The trade-off for this increased speed, of course, is an increase in code size, as a different version of the function will be created for each different iteration count.

Non-type template parameters are particularly useful when utilising templates with classes, as discussed in the section that follows.


Templates and classes
=====================

In addition to template functions, programmers can also specify template classes, like so:

```cpp
template <typename T>
class Container
{
	protected:
		T* items;
		
	public:
		Container(int size) {
			this->items = new T[size];
		}
		
		~Container() {
			delete[] this->items;
		}
		
		T& operator[](int index) {
			return this->items[index];
		}
};
```

When instantiating template classes, the values for the template parameters must be specified explicitly, like so:

```cpp
int main()
{
	Container<int> intContainer;        //Version for ints
	Container<double> doubleContainer;  //Version for doubles
	Container<char> charContainer;      //Version for chars
	
	return 0;
}
```

The existing implementation of the `Container` class allocates memory on the heap. This is necessary because the size is passed at run-time to the constructor, and memory can only be allocated on the stack when the size is known at compile-time. However, the use of a non-type template parameter allows us to circumvent this limitation:

```cpp
template <typename T, int size>
class Container
{
	protected:
		T items[size];
		
	public:
		T& operator[](int index) {
			return this->items[index];
		}
};
```

This updated version of the `Container` class allocates memory on the stack, and is simpler due the fact that we no longer need to allocate and deallocate memory in the constructor and destructor, respectively. We specify the size of the array at compile-time, when instantiating the class:

```cpp
int main()
{
	Container<int, 10> intContainer;       //Version for 10 ints
	Container<double, 7> doubleContainer;  //Version for 7 doubles
	Container<char, 5> charContainer;      //Version for 5 chars
	
	return 0;
}
```


Template classes and inheritance
--------------------------------

Classes can inherit from template classes, like so:

```cpp
template <typename T, int size>
class SummableContainer : public Container<T, size>
{
	public:
		virtual T sum() const
		{
			T result = 0;
			
			for (int i = 0; i < size; ++i) {
				result += this->items[i];
			}
			
			return result;
		}
};
```

The above class is itself a template class, and specifies the relationship between its own template parameters and those of its superclass through the part of the declaration that specifies `public Container<T, size>`.

Classes can also specify values for one or more of the template parameters of their superclasses:

```cpp
template <typename T>
class ContainerThatAlwaysHoldsFive : public Container<T, 5>
{
	//...
};
```

Classes that specify values for all of the template parameters of their superclasses may not be template classes themselves:

```cpp
//Note the lack of a template<...> declaration - this is not a template class

class ContainerThatAlwaysHoldsFiveIntegers : public Container<int, 5>
{
	//...
};
```

Of course, some classes may specify values for all of the template parameters of their superclasses, but still be template classes themselves, due to introducing new template parameters:

```cpp
template <typename ExtraValType>
class ContainerThatAlwaysHoldsFiveIntegersAndOneExtraValue : public Container<int, 5>
{
	protected:
		ExtraValType extraValue;
	
	//...
};
```


Template classes and specialisation
-----------------------------------

Template specialisation also works with template classes. A simple example is shown below:

```cpp
//Original template class
template <typename T>
class SimpleClass
{
	//...
};

//Template specialisation for int
template<>
class SimpleClass<int>
{
	//...
};
```

For an example of partial template specialisation when using inheritance, consider the `SummableContainer` class from the earlier example:

```cpp
template <typename T, int size>
class SummableContainer : public Container<T, size>
{
	public:
		T sum() const
		{
			T result = 0;
			
			for (int i = 0; i < size; ++i) {
				result += this->items[i];
			}
			
			return result;
		}
};
```

The `sum()` method of this class works correctly for all types that can be safely assigned the value zero (**`0`**), such as primitive types. However, if we wanted to create a version that held strings (in the form of the [`std::string`](http://www.cplusplus.com/reference/string/string/) class), the line of code that assigns zero to `result` would cause a runtime error. We can create a specialisation of the class for `std::string` instances like so:

```cpp
#include <string>

template <int size>
class SummableContainer<std::string, size> : public Container<std::string, size>
{
	public:
		std::string sum() const
		{
			std::string result = "";
			
			for (int i = 0; i < size; ++i) {
				result += this->items[i];
			}
			
			return result;
		}
};
```

When we create an instance of `SummableContainer<std::string>`, the template specialisation above will be utilised. The `sum()` method will function correctly, and behave as it does for other types, by adding items together using the plus-equals operator (`+=`). In the case of strings, this will perform concatenation, and the result will be one long string containing all of the items in the container back-to-back.


Header-only approach using templates
====================================

Templates, including template functions and classes, and both full or partial template specialisations that are themselves templates, **must be placed in header files**. Template code is not compiled separately in a `.cpp` source file and then linked against later. This is because the compiler generates the filled-out versions of template code as needed, which precludes compiling template code in the same manner as ordinary code.

A practical upshot of this requirement is that template code can be distributed easily in header form, and client code need simply include the relevant headers to utilise the templates, without the need for separate compilation and linking. The downside of this, of course, is that it becomes particularly important to properly unit-test template code, since only the filled-out versions that are actually generated will be compiled. Bugs can easily hide in versions of template code that are never used, because they are never compiled.

It is possible to exploit the header-only nature of templates to create code libraries that are completely header-only. Consider the following class:

```cpp
class SomeArbitraryClass
{
	public:
		int num;
};
```

Although there is no need for this class to be a template, we can make it one quite easily:

```cpp
template <typename T>
class SomeArbitraryTemplateClass
{
	public:
		T num;
};
```

Of course, this discards the information that the member field `num` is supposed to be of type `int` by design. However, we can create a handy type alias using the C/C++ `typedef` keyword:

```cpp
//Equivalent to:
#define SomeArbitraryClass SomeArbitraryTemplateClass<int>

typedef SomeArbitraryTemplateClass<int> SomeArbitraryClass;
```

The `typedef` keyword creates a compile-time alias for a type. In the example, we have created the alias `SomeArbitraryClass` to refer to the `int` version of `SomeArbitraryTemplateClass`. We can then instruct the users of our code library to utilise the `SomeArbitraryClass` type, which will automatically be substituted to become `SomeArbitraryTemplateClass<int>` by the compiler.


Limitations and caveats of type deduction
=========================================

No ambiguity allowed
--------------------

Limitations regarding ambiguity arise when using [**overload resolution**](http://en.cppreference.com/w/cpp/language/overload_resolution), the process used by the compiler when working with overloaded functions. A sequence of techniques, including type deduction when templates are involved, is used to select the correct version of an overloaded function to use, based on the arguments supplied in the function call. When an **implicit cast** is available from one type to another, this will be taken into consideration when selecting the correct version of the overloaded function to use. This can be extremely convenient. However, this functionality also presents opportunities for ambiguity.

Due to the availability of implicit casts, some method overloads can be considered equally good candidates for selection. For example, consider the method overloads below:

```cpp
void func(float, double);
void func(double, float);
```

Now consider the function call below:

```cpp
func(0.5, 1.5);
```

Which version of the method should the compiler use? Both arguments are `double` literals, which are implicitly castable to `float`. However, `float` is in turn implicitly castable to `double`. The method overloads are considered to be **ambiguous**, as the compiler is unable to make a clear decision regarding which version to use. Because **ambiguous overloads are forbidden in C++**, the function call will result in a compiler error.

The same issue arises when creating template specialisations. It is important to take care that no set of specialisations are ambiguous. If they are, calls to any of those specialisations can cause compiler errors.


Implicit cast behaviour during template argument type deduction
---------------------------------------------------------------

Consider the following code:

```cpp
#include <iostream>

void func(const std::string& str);

int main()
{
	//Implicit cast from const char* to std::string
	func("Hello World!");
	
	return 0;
}

```

String literals in C and C++ are represented as "C-strings" - [null-terminated character arrays](http://en.wikipedia.org/wiki/Null-terminated_string). A string literal evaluates to a pointer to the start of the `char` array, and code reading the C-string simply reads characters from the array until a null byte is encountered, signalling the end of the string. C-strings are extremely primitive, and suffer from a number of serious limitations. This makes working with strings in C tedious and error-prone. Fortunately, the C++ `std::string` class provides an extremely useful and flexible string type for use in C++.

Because an implicit cast from `const char*` to `std::string` exists (int the form of the `std::string` constructor that takes a `const char*` as an argument), the code above will cast the string literal `"Hello World!"` to a `std::string` instance and pass it to the `func()` function.

However, consider the following code:

```cpp
#include <string>

template <typename T>
T add(T a, T b) {
	return a + b;
}

int main()
{
	//Compiler error
	add("Hello", "World!");
	
	return 0;
}
```

When using templates, no implicit cast is utilised. This is because argument type deduction determines that the type of the argument is `const char*` and creates a version of the template function `add()` filled-out with `const char*` as the value for the template parameter `T`. Of course, the addition operator (`+`) is not by default defined for instances of `const char*`, resulting in a compiler error.

In this case, it is necessary to **explicitly** construct a `std::string` instance from the string literal, like so:

```cpp
int main()
{
	//Works correctly now
	add(std::string("Hello"), std::string("World!"));
	
	return 0;
}
```
