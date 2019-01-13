---
layout: default
title: C++ Workshop 4 - Pointers and Memory Management, Part 2
description: "Memory management"
permalink: cplusplusworkshop4-2.html
---

# C++ Workshop 4 - Pointers and Memory Management, Part 2

Safe pointers: references
=========================

In addition to ordinary pointers from C, C++ introduces a "safe pointer" type known as a **reference**. References are pointers that adhere to the following restrictions:

- A reference can only ever point to **one memory location**, and cannot be changed to point at another location.
- A reference must be set to a memory location when it is created. A reference cannot be created without being initialised.
- References are automatically dereferenced. Once a reference is bound to a memory location, it is manipulated as though it were its pointee. All operators, including but not limited to, assignment, increment, addressof, and the dot operator, operate directly on the pointee.

These rules are demonstrated by the code example below.

```cpp
#include <iostream>

int main()
{
	int a = 2;
	int b = 1;
	
	//Pointers are declared using the * character
	//Pointers require the addressof operator when pointing to a stack variable
	int* ptr = &a;
	
	//References are declared using the & character
	//References do not use the addressof operator
	int& ref = a;
	
	//Pointers must be dereferenced to modify the pointee
	*ptr = 3;
	
	//References are dereferenced automatically (this line is equivalent to a = 4)
	ref = 4;
	
	//Will output 4, as the value of a was modified on the previous line through the reference
	std::cout << a << std::endl;
	
	//Pointers can be changed to point to a different memory address
	ptr = &b;
	
	//References cannot be changed to point to a different pointee.
	//Assignment always modifies the pointee, not the reference (this line is equivalent to a = b)
	ref = b;
	
	//References cannot be created without being initialised
	int& otherRef; //ILLEGAL! Compiler error.
	
	return 0;
}
```
**Listing 1: example of references**

References are far safer than normal pointers, since they cannot be `NULL` and can always be guaranteed to point to the same pointee. They also offer the convenience of simpler syntax for using pointees, since dereferencing is automatic. Of course, the restrictions of references make them far less flexible than normal pointers. The choice of whether to use a pointer or a reference **depends on whether or not a pointer needs to be able to change the memory address it points to**.


Pass-by-value, pass-by-pointer, and pass-by-reference
=====================================================

When passing arguments to functions in C++, there are, broadly speaking, three options available:

- Pass by **value**: the value of the original data is copied. The copy is entirely separate to the original data, and cannot modify it in any way.
- Pass by **pointer**: a pointer containing the memory address of the original data is passed. The pointer can be dereferenced to directly access the original data. No data is copied.
- Pass by **reference**: a reference bound to the original data is passed. The reference is automatically dereferenced, and any operations on the reference are automatically applied to the original data. No data is copied.

The code example below demonstrates the three argument passing options:

```cpp
#include <iostream>

//Pass-by-value
void byVal(int b) {
	b = 5;
}

//Pass-by-pointer
void byPtr(int* b) {
	*b = 5;
}

//Pass-by-reference
void byRef(int& b) {
	b = 7;
}

int main()
{
	int a = 2;
	
	//Cannot modify a
	byVal(a);
	std::cout << a << std::endl;
	
	//Can modify a
	byPtr(&a);
	std::cout << a << std::endl;
	
	//Can modify a
	byRef(a);
	std::cout << a << std::endl;
	
	return 0;
}
```

Immutability: the `const` keyword
=================================

Although passing by pointer or reference is extremely memory efficient due to avoiding having to copy data, there are cases where it is desirable that a function not be able to modify the original data.

To this end, C-family languages (C, C++, and Objective-C) feature a keyword for specifying that data is immutable - the `const` keyword. This language feature is omitted in a number of other programming languages (or featured in a far less powerful form, such as `final` in Java), so it may be entirely unfamiliar to readers who have never used C-family languages before.

The code example below demonstrates use of the `const` keyword:

```cpp
#include <iostream>

void byPtr(const int* arg) {
	*arg = 7; //Not allowed
}

void byRef(const int& arg) {
	arg = 7; //Not allowed
}

void constPtrExample(int* const ptr)
{
	*ptr = 9;   //Allowed
	ptr = NULL; //Not allowed
}

int main()
{
	//Const isn't just for function arguments, it can also be specified for variables
	const int immutable = 2;
	immutable = 3; //Not allowed
	
	int var = 5;
	const int* ptr = &var;
	*ptr = 7; //Not allowed
	
	const int& ref = var;
	ref = 7; //Not allowed
	
	byRef(var);
	byPtr(&var);
	
	constPtrExample(&var);
	
	return 0;
}
```
**Listing 2: example usage of the const keyword**

The `const` keyword can be specified for both ordinary variables, and for pointers and references.

When `const` is specified in a **variable** declaration, that variable must be initialised with an initial value when it is declared. The value of that variable is then immutable, and cannot be changed at any subsequent point without the use of a pointer and a cast to remove `const`-ness (discussed later in the workshop). 

When `const` is specified in a **pointer** declaration, **the meaning is dependant upon the placement of the `const` keyword**:

- When declared to the **left** of the asterisk, the `const` keyword specifies that the **pointee** be treated as immutable:
  ```cpp
  const int* ptr1; //Pointee is immutable
  int const* ptr2; //Pointee is immutable
  ```
- When declared to the **right** of the asterisk, the `const` keyword specifies that the **pointer itself** be treated as immutable:
  ```cpp
  int* const ptr3; //Pointer is immutable
  ```
- These can also be combined to specify that both the pointer and pointee are immutable:
  ```cpp
  const int* const ptr4; //Both pointer and pointee are immutable
  int const* const ptr5; //Both pointer and pointee are immutable
  ```
- This behaviour is also consistent regardless of the level of indirection:
  ```cpp
  const int * const * ptr6; //Pointer to a const pointer to a const int
  int const * * const ptr7; //Const pointer to a pointer to a const int
  int * const * const ptr8; //Const pointer to a const pointer to an int
  int * * const ptr9;       //Const pointer to a pointer to an int
  ```

([This StackOverflow answer](http://stackoverflow.com/questions/1143262/what-is-the-difference-between-const-int-const-int-const-int-const/1143272#1143272) also describes the combinations of `const` placement quite nicely.)

When `const` is specified in a **reference** declaration, it can only refer to the pointee, as a reference itself is already immutable due to the restriction of only ever pointing to one pointee.


Passing r-values by `const` reference
------------------------------------

From the perspective of assignment, there are two types of values:
- An **l-value** is any value that can appear on the left-hand side of an assignment expression. L-values refer to data **that can be modified**, and include variables, pointers or references to data, and expressions that evaluate to pointers or references to data.
- An **r-value** is any value that can appear on the right-hand side of an assignment expression. **All l-values also qualify as r-values.** However, r-values can also include values that **cannot be modified**, such as literals, constant variables, references and pointers, and temporary values that are the results of expressions, but have not been assigned to a storage location. The compiler allocates temporary storage locations for such temporary variables, but does not allow them to be the target of assignment or the addressof operator.

R-values that are not l-values cannot be passed by pointer, because the addressof operator cannot be used on compiler-generated temporary storage locations. (Attempting to do so will result in a compiler error stating that an l-value is required.) Although r-values that are not l-values can always be passed by value, doing so incurs memory overhead due to copying the data.

To avoid needless copying of temporary data, **C++ allows constant references to point to r-values that are not l-values**. Because both the reference itself and the pointee are immutable, there is no danger of the programmer accidentally losing data by assigning it to the temporary storage location and forgetting to copy it to a non-temporary location.

The code example below demonstrates this usage:

```cpp
void func(const int& a) {
	//Do stuff with the value of a
}

int main()
{
	int num = 5;
	const int constNum = 7;
	
	//These are all legal
	func(num);               //Variable
	func(constNum);          //Const variable
	func(10);                //Literal
	func(num + constNum);    //Temporary result of expression
	
	return 0;
}
```


The `const` keyword and classes
-------------------------------

The `const` keyword has special interactions when working with classes in C++. **Programmers are able to mark methods of a class as `const`**, like so:

```cpp
class MyClass
{
	private:
		int data;
		
	public:
		
		//Not const
		MyClass() {
			this->data = 0;
		}
		
		//Not const
		void setData(int val) {
			this->data = val;
		}
		
		//Const
		int getData() const {
			return this->data;
		}
};
```

Then, when working with `const` instances of the class, or `const` references or pointers to an instance of the class, **the compiler only allows methods marked as const to be called**. This behaviour is demonstrated by the code example below:

```cpp
#include <iostream>

class MyClass
{
	private:
		int data;
		
	public:
		
		//Not const
		MyClass() { this->data = 0; }
		
		//Not const
		void setData(int val) { this->data = val; }
		
		//Const
		int getData() const { return this->data; }
};

int main()
{
	//Create non-const instance, pointer, and ref
	MyClass inst;
	MyClass* ptr = &inst;
	MyClass& ref = inst;
	
	//Create const instance, pointer, and ref
	const MyClass constInst;
	const MyClass* constPtr = &constInst;
	const MyClass& constRef = constInst;
	
	//For non-const instance, pointer and ref, all methods are allowed
	inst.setData(9);
	inst.getData();
	ptr->setData(9);
	ptr->getData();
	ref.setData(9);
	ref.getData();
	
	//For const instance, pointer and ref, const methods are allowed
	constInst.getData();
	constPtr->getData();
	constRef.getData();
	
	//For const instance, pointer and ref, non-const methods are NOT allowed
	//ILLEGAL! Compiler error for all three lines.
	constInst.setData(7);
	constPtr->setData(7);
	constRef.setData(7);
	
	return 0;
}
```

In the vast majority of cases, it is recommended to respect the `const`-ness of data. However, if it is necessary to do so, **the `const` qualifier(s) of a pointer or reference can be removed by using a cast**, like so:

```cpp
//Cast const qualifier(s) away
((MyClass*)constPtr)->setData(7);
((MyClass&)constRef).setData(7);

//Alternative syntax (equivalent to the lines above)
(const_cast<MyClass*>(constPtr))->setData(7);
(const_cast<MyClass&>(constRef)).setData(7);
```


Manual memory management issues: memory ownership
=================================================

Whenever memory is allocated on the heap using the `new` keyword, it must be deallocated by a corresponding call to the `delete` keyword. That is:

- **Every call to `new` must be subsequently followed by *exactly one* matching call to `delete`.**

Failure to follow this rule will result in different issues, depending on the manner in which the rule is ignored:

- Not calling `delete` at all on an allocated block of memory will result in a **memory leak**.
- Attempting to call `delete` more than once on an allocated block of memory is known as a **double free**, and can cause corruption of memory management structures, leading to unstable behaviour, crashes, and vulnerability to malicious code exploits.

Although each `new` call should have one corresponding `delete` call at runtime, in a program's source code, there may be multiple `delete` statements corresponding to a single `new`:

```cpp
//This function allocates memory, which is then owned by the calling function
int* func()
{
	int* i = new int;
	return i;
}

void otherFunc()
{
	//Our responsibility to free allocated memory
	int* i = func();
	delete i;
}

int main()
{
	otherFunc();
	
	//Our responsibility to free allocated memory
	int* i = func();
	delete i;
	
	return 0;
}
```
**Listing 3: example of multiple delete statements in a program's source code that correspond to a single new statement**

As can be seen in the example above, although each `new` call in `func()` is paired with a corresponding `delete` call at runtime, there are multiple places in the source code where the `delete` statement exists. This is because each caller of `func()` **owns the memory** allocated by it, and has the responsibility to free that memory.

There are other cases where a caller **does not own the memory** it receives a pointer to:

```cpp
#include <iostream>

class MyClass
{
	private:
		int* data;
		
	public:
		MyClass() {
			this->data = new int;
		}
		
		//Destructor, called when object is destroyed,
		//either via delete when allocated on the heap,
		//or when the instance variable goes out of scope when allocated in a stack frame
		~MyClass() {
			delete this->data;
		}
		
		int* getData() const {
			return this->data;
		}
};

int main()
{
	//Our responsibility to free
	MyClass* m = new MyClass();
	
	//NOT our responsibility, owned by m
	int* intPtr = m->getData();
	
	//This will cause problems! The destructor for m will call delete again, resulting in a double-free
	delete intPtr;
	
	//Calls m destructor
	delete m;
	
	return 0;
}
```

In the example above, the instance of `MyClass` owns the memory allocated in its constructor, and frees it in its destructor. **The destructor is called when the object is destroyed**, through the following cicumstances:

- If the instance of the class was allocated on the **stack**, it will be destroyed (and the destructor called) when it goes out of scope.
- If the instance of the class was allocated on the **heap**, it will be destroyed (and the destructor called) when `delete` is called to deallocate it.

The code examples shown in this section demonstrate the importance of knowing exactly which part of the code owns each piece of memory allocated on the heap. This is a common source of errors when programming in C++, and highlights the diligence that is required when utilising manual memory management. Traditionally, this has simple been the price to pay for access to low-level efficiency.

In recent years, however, C++ has added features to simplify memory management, and reduce the burden on programmers. These features are discussed in the section that follows.


C++11 Smart Pointers
====================

[C++11](http://en.wikipedia.org/wiki/C%2B%2B11) is the version of the C++ programming language standard that was finalised in late 2011. Amongst the many new additions to the language over the previous version, are "**smart pointers**" - wrappers for raw pointers that provide simplified memory management.

At the time of writing, C++11 support is available in both [GCC](https://gcc.gnu.org/projects/cxx0x.html) and [Clang](http://clang.llvm.org/cxx_status.html), but requires an explicit command-line argument to enable C++11 mode in certain versions of both compilers. The command-line flag to enable C++11 mode is as follows (**substitute `g++` for `clang++` if using Clang**):

```
g++ -std=c++11 <other arguments>
```

When C++11 mode is enabled, all new language features and header files become recognised. **Attempting to compile code using C++11 features or header files when under the older C++03 mode will result in compiler errors.**

C++11 smart pointers are found in the header file `<memory>`, which must be included using a preprocessor `#include` directive prior to using any smart pointer types. There are three smart pointer types, which are each discussed below.

Unique pointers: `std::unique_ptr`
----------------------------------

Unique pointers are useful when the code that allocates a piece of memory is also the owner of that memory. The [`std::unique_ptr`](http://www.cplusplus.com/reference/memory/unique_ptr/) wrapper type will simply free the wrapped pointer when the wrapper is destroyed. This behaviour is demonstrated by the code example below.

```cpp
//THIS CODE MUST BE COMPILED IN C++11 MODE, USING -std=c++11 FOR COMPILERS THAT DEFAULT TO C++03 MODE
#include <memory>

int main()
{
	//Allocated memory will be automatically freed when wrappedPtr goes out of scope
	std::unique_ptr<int> wrappedPtr( new int );
	
	//We can dereference the wrapped pointer just as we would a raw pointer
	*wrappedPtr = 5;
	
	return 0;
}
```

To facilitate interoperability with older code that uses raw pointers, the wrapped pointer can be retrieved using the `get()` method. If desired, the wrapper can also be instructed to relinquish ownership of the wrapped pointer, through the `release()` method:

```cpp
//THIS CODE MUST BE COMPILED IN C++11 MODE, USING -std=c++11 FOR COMPILERS THAT DEFAULT TO C++03 MODE
#include <memory>

int main()
{
	std::unique_ptr<int> wrappedPtr( new int );
	
	//This just retrieves the pointer, it is still owned by the unique_ptr
	int* rawPtr = wrappedPtr.get();
	
	//This takes ownership of the memory, meaning we have to free it ourselves
	int* ptr = wrappedPtr.release();
	delete ptr;
	
	return 0;
}
```

If other parts of the program's code need to access the memory, the raw pointer can simply be supplied by the owner. It is common that the owner of the memory holds the `std::unique_ptr` instance, and other code may request the raw pointer.

Shared pointers: `std::shared_ptr`
----------------------------------

In some cases, multiple parts of a program's code may not only need to *access* a single piece of memory, but require **shared ownership** of the memory. In a shared ownership scenario, the memory can only be freed once it is no longer needed by any of the owners.

To facilitate shared ownership, C++11 includes the [`std::shared_ptr`](http://www.cplusplus.com/reference/memory/shared_ptr/) smart pointer type. Shared pointers are created using the `std::make_shared()` function, like so:

```cpp
//THIS CODE MUST BE COMPILED IN C++11 MODE, USING -std=c++11 FOR COMPILERS THAT DEFAULT TO C++03 MODE
#include <memory>

int main()
{
	//Reference counted, freed when no more references exist
	std::shared_ptr<int> sharedData = std::make_shared<int>();
	
	//We can dereference the wrapped pointer just as we would a raw pointer
	*sharedData = 5;
	
	//This just retrieves the pointer, ownership is still shared amongst all holders of the shared_ptr
    int* rawPtr = sharedData.get();
	
	return 0;
}
```

When multiple parts of the program need ownership of the wrapped pointer, they must copy the `std::shared_ptr` wrapper. Retrieving a copy of the raw pointer will not confer ownership.

Ownership of the memory is tracked through the use of a **reference count**. Whenever a copy of the `std::shared_ptr` wrapper is made, the global reference count for the wrapped pointer is incremented by one. Whenever one of the `std::shared_ptr` wrappers is destroyed, the reference count is decremented by one. **When the reference count falls to zero, the memory is freed.** This is because the runtime knows that no more "owning" copies of the pointer exist.

Copies of the raw pointer do not influence the reference count, and will become invalid after the memory has been freed. Accordingly, any code that wishes to retain ownership of the memory must hold a `std::shared_ptr` wrapper.

Weak pointers: `std::weak_ptr`
------------------------------

If code wishes to hold a copy of a pointer to shared data, **but not hold ownership of it**, the [`std::weak_ptr`](http://www.cplusplus.com/reference/memory/weak_ptr/) wrapper type can be used. A `std::weak_ptr` does not influence the reference count of the wrapped pointer, and may point to either valid memory, or invalid memory that has been freed due to the reference count falling to zero. **The `std::weak_ptr` wrapper can be queried to determine if the memory it points to is still valid.** If the memory has not been freed yet, then it can then be accessed.

Weak pointers are used to solve an inherent limitation of reference counting systems - that of **circular references**. If two objects contain mutual references to one another, then the reference counts of both objects will never fall to zero, even once all other owners of both objects have relinquished ownership of them. The circular reference cycles are broken by using "weak references" (in C++, instances of `std::weak_ptr`.)


Reference counting .vs. mark-and-sweep algorithms
=================================================

C++11 smart pointers only support one type of automatic memory management - that of **automatic reference counting**. This is the same type of memory management used in Objective-C when targetting the Apple iOS platform.

Non-native languages commonly utilise a more comprehensive runtime memory management system known as a **garbage collector**. The garbage collector periodically identifies memory which is no longer being used, and frees it. Garbage collectors may utilise different implementations for identifying memory that can be freed. Two prominent approaches are:

- **Reference counting**: when automatic reference counting is used in C++, memory is freed instantly when its reference count falls to zero. When the reference count falls to zero in a garbage-collected system, the memory is instead marked for deletion. It is then subsequently freed by the garbage collector then next time it runs. **In both garbage-collected and non-garbage-collected systems, circular references are a problem, and the programmer must use weak references to break any cycles.**
- **Mark-and-sweep**: to overcome the inherent limitations of reference counting, a technique known as mark-and-sweep is often utilised instead. A graph is constructed that represents all of the references in the program. Reachability analysis is then utilised to determine which objects are reachable from the "root" nodes (which typically correspond to the global memory scope.) The unreachable objects are then marked for deletion. This overcomes the limitation of circular references, as objects which are referenced only by other unreachable objects will still be correctly identified as unreachable. This is depicted in Figure 1.

![Object reachability graph](./images/mark-and-sweep.png "Figure #num: example of an object reachability graph, as would be constructed by a mark-and-sweep algorithm")

**Mark-and-sweep is more computationally expensive than reference counting, but more convenient for programmers.** Some languages, such as Java, support both modes of garbage collection operation, and provide an option to configure which implementation is used. (*Unfortunately for Java programmers, they must always break cycles using weak references, in order to support the situation when reference counting is used, removing any convenience offered by including the mark-and-sweep option.*)

Fortunately, features such as memory compaction (which relocates objects into contiguous memory to remove fragmentation and improve memory access locality) are able to offer performance improvements that partially offset the cost of running the garbage collector itself. This allows expensive garbage collection implementations, such as mark-and-sweep, to offer reduced programmer burden whilst remaining viable from a performance perspective.

For more information on the differences between reference counting and mark-and-sweep, [the MDN article on memory management in Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management) provides a good overview.
