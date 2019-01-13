---
layout: default
title: C++ Workshop 3 - Pointers and Memory Management, Part 1
description: "Memory layout and storage"
permalink: cplusplusworkshop3-1.html
---

# C++ Workshop 3 - Pointers and Memory Management, Part 1

Memory layout and storage
=========================

At runtime, applications typically store data in two regions of memory: the **stack**, and the **heap**.

The Stack
---------

The stack is a fixed-size region of memory that is pre-allocated for each thread in an application's executing process. The stack is used for storing data **local to each thread**, and typically ranges in size from around 1 megabyte to 10 megabtyes, depending on platform.

The stack is so named because it is an instance of the data structure known as a *stack*, which is a **Last-In First-Out (LIFO)** data structure. Items are "pushed" on to the top of a stack, and "popped" off from the top of a stack. Only the item at the top of a stack can be accessed - to access an item "below" the top, all of the items "above" the target item must be popped from the stack first.

![Simple representation of a stack](https://upload.wikimedia.org/wikipedia/commons/2/29/Data_stack.svg "Figure 1: simple representation of a stack, from the Wikipedia article on the stack data structure")

The stack can be manipulated directly when programming in machine code or assembly (for example, x86 assembly includes instructions for pushing and popping named `push` and `pop`, respectively.) When programming in Third-Generation Languages (3GL), however, **direct manipulation of the stack is typically the responsibility of the compiler**. This is due to the role the stack plays in the implementation of core language features, most notably function calls.

The stack is also referred to as the "**call stack**". Almost all modern programming languages represent the information about a function call as an item on the call stack, typically referred to as a **stack frame**. When a function is called, a stack frame is created for that invocation of the function, and pushed to the stack. When the function returns, the stack frame for the current invocation is popped from the top of the stack, and control returns to the function represented by the stack frame below it. This layout is depicted in Figure 2:

![Layout of the call stack](https://upload.wikimedia.org/wikipedia/commons/d/d3/Call_stack_layout.svg "Figure 2: layout of the call stack, from the Wikipedia article on the call stack")

To facilitate correct execution, each stack frame includes a "**return address**", which is used to resume execution of the *caller* function after the *callee* function returns. This concept is illustrated by the comments in the code listing below:

```cpp
void func()
{
	/*
		Do stuff, and then return.
		Execution will resume with the statement immediately AFTER the function call to func().
	*/
}

int main()
{
	int a = 0;
	
	//When we call func() this time, the return address is the statement a = 1
	func();
	a = 1;
	
	//When we call func() this time, the return address is the statement a = 2
	func();
	a = 2;
	
	//When we call func() this time, the return address is the statement a = 3
	func();
	a = 3;
	
	return 0;
}
```
**Listing 1: code illustrating the use of the "return address"**

The stack frame may contain the values of the function's formal parameters. For value-returning functions, the stack frame may also include information about where to store the return value. The storage location of both formal parameters and return value is implementation-defined.

The bulk of the contents of each stack frame are the **local variables** for the function in question. All variables declared within the scope of a function are stored within its stack frame. This means the programmer does not need to worry about allocating or deallocating memory for local variables, as they are created and destroyed automatically when the stack frame is created and destroyed.

For example, consider the following function:

```cpp
int func()
{
	int a = 1;
	int b = 2;
	int c = 3;
	
	return a + b + c;
}
```

We can expect the stack frame for a call to this function to follow a structure along the lines of the following (as a minimum):

|Memory offset<br />(from start of stack frame)|Contents|
|:----------:|:------:|
|0x000c| c |
|0x0008| b |
|0x0004| a |
|0x0000|Return address|

The above table assumes a 32-bit return address. It is important to note that the order of `a`, `b`, and `c` will be implementation-defined, and **cannot be assumed to follow any specific ordering** across platforms and compilers.


The Heap
--------

The heap (**not related to the data structure known as a *heap***) represents the pool of memory available to all processes running on a host machine. The heap is used for storing data that is **shared between all of an application's threads**. The size of the heap is bounded by the amount of virtual memory that is managed by the host operating system, and typically reflects the amount of physical RAM installed within the host machine's hardware configuration.

Applications allocate memory on the heap by **requesting** it from the operating system. This is done via a system call, and must specify the quantity of memory that is to be allocated. The operating system maintains information on the regions of memory that are available, and will allocate the first contiguous region of available memory that satisfies the requested size.

Due to the overhead involved in performing system calls, allocation of memory on the heap is more computationally expensive than storing data on the stack. In particular, fewer allocation requests for larger blocks of memory are more efficient than a large number of allocation requests for small blocks of memory. This is because the use of fewer allocation requests results in fewer system calls.

**Fun fact:** *on some embedded system platforms, heap allocation is so prohibitively expensive that only the stack is ever used. This requires programmers to perform all manner of tricks and techniques to accommodate the limitations of purely stack-based storage. Fortunately, modern consumer devices, including mobile devices such as smartphones and tablets, do not suffer this limitation, and heap allocation can be used on servers, desktops, laptops, and mobile devices alike*.


Pointers
========

Pointers are the mechanism through which memory can be manually managed in C and C++. A pointer is, in essence, simply **a primitive type which stores a memory address**. In addition to their value (the memory address), **pointers also have a type**. However, this type does not influence the pointer itself, but rather specifies how **the data at the specified memory location** should be treated.

When code has been compiled for a 32-bit architecture, pointers are 32 bits (4 bytes) in size. When code has been compiled for a 64-bit architecture, pointers are 64 bits (8 bytes) in size. Aside from this difference in size, pointers behave the same way across all architectures.

To reiterate, **pointers consist of two things:**

- **A value**, which is the memory address of some data
- **A type**, which specifies how the data at the specified memory address should be treated


Video: Pointer Fun with Binky
-----------------------------

Below is a video produced by Stanford University in 1999 that demonstrates the key principles of using pointers in C++:

<iframe width="640" height="480" src="https://www.youtube.com/embed/i49_SNt4yfk?rel=0" frameborder="0" allowfullscreen="allowfullscreen"></iframe>

The code below demonstrates the points discussed in the video:

```cpp
#include <iostream>

int main()
{
	//Both x and y point to the same memory location...
	int* x = new int;
	int* y = x;
	
	//...so the two memory addresses printed by these statements will be identical.
	std::cout << x << std::endl;
	std::cout << y << std::endl;
	
	//Dereferencing either pointer will manipulate the same data...
	*x = 16;
	*y = 12;
	
	//...so the value 12 will be printed out by both of these statements.
	std::cout << *x << std::endl;
	std::cout << *y << std::endl;
	
	delete x;
	return 0;
}
```

When the code above is run, the pointers `x` and `y` both point to the same location in memory. Therefore, changes to the data pointed to by one pointer are reflected when dereferencing the other.


Core pointer operations: dereference and addressof
--------------------------------------------------

In addition to the **dereference** operator (`*`), which follows a pointer to its pointee, there is also the "**addressof**" operator (`&`), which retrieves the memory address of a variable so that it can be stored in a pointer.

Using the *addressof* operator allows pointers to point to data on the stack, as well as memory allocated on the heap using the `new` operator. The example code below demonstrates this behaviour.

```cpp
#include <iostream>

int main()
{
	//a is a local stack variable
	int a = 5;
	
	//Here we make y point to a
	int* y = &a;
	
	//Accordingly, dereferencing y allows us to modify the contents of a
	*y = 6;
	
	//These statements both print the memory address of a
	std::cout << &a << std::endl;
	std::cout << y << std::endl;
	
	//These statements both print 6, which is the value of a after we modified it by dereferencing y
	std::cout << a << std::endl;
	std::cout << *y << std::endl;
	
	return 0;
}
```

By retrieving the memory address of the local stack variable `a` and storing it in a pointer, subsequent code is able to modify the contents of `a` either directly or by dereferencing the pointer to it.


Pointers to pointers, and arbitrary levels of indirection
---------------------------------------------------------

The type of a pointer specifies the datatype of its pointee. This datatype can be anything, including a pointer. In this manner, we can have pointers to pointers, or pointers to pointers to pointers, and so on. **The dereference and addressof operators behave the same way, regardless of the level of indirection.** To clarify this concept, Figure 3 employs a "planes" metaphor to depict the behaviour of these operators at different levels of indirection. (Thanks to Colin Lemmon.)

![Pointer planes diagram](./images/pointer_planes.png "Figure #num: behaviour of the dereference and addressof operators at different levels of indirection")

This behaviour is further demonstrated by the code example below:

```cpp
#include <iostream>

int main()
{
	//Create the pointers x and y
	int* x = new int;
	int* y;
	
	//Create the pointer to a pointer, z, and point it to y
	int** z = &y;
	
	//Set y to the same value as x, by dereferencing z
	*z = x;  //This is equivalent to y = x
	
	//These statements both print the memory address of y
	//(NOTE: this is the memory address of y itself on the stack, not the memory address it points to!)
	std::cout << &y << std::endl;
	std::cout << z << std::endl;
	
	//At this point, x and y point to the same data, just as in the earlier example.
	//Therefore, dereferencing either pointer will manipulate the same data.
	*x = 16;
	*y = 12;
	
	//As in the earlier example, the value 12 will be printed out by both of these statements.
	std::cout << *x << std::endl;
	std::cout << *x << std::endl;
	
	delete x;
	return 0;
}
```


Pointers to objects
-------------------

Pointers to object behave the same way as pointers to primitives. When dereferenced, the object can be manipulated as usual. However, due to operator associativity, the dereference operator needs to be used in a parenthesised sub-expression to correctly work with the dot operator, like so:

```cpp
//Won't work
*ptr.member

//Will work correctly
(*ptr).member
```

To avoid this verbose syntax, C and C++ support a syntactic sugar for it, in the form of the **arrow operator** (`->`):

```cpp
//Equivalent to (*ptr).member
ptr->member
```

The code example below demonstrates the use of pointers to objects and the arrow operator:

```cpp
#include <iostream>

class Dummy
{
	public:
		int a;
		int b;
		
		void change() {
			a = 9;
		}
};

int main()
{
	//Create an object by instantiating the Dummy class
	Dummy object;
	object.a = 5;
	object.b = 7;
	
	//Create a pointer to the object
	Dummy* objectPtr = &object;
	
	//Create a pointer to one of the object's primitive member fields
	int* primitivePtr = &(object.a);
	
	//We can directly manipulate public member fields by dereferencing pointers to them
	*primitivePtr = 8;
	std::cout << object.a << std::endl;
	
	//We can also manipulate the object itself by dereferencing a pointer to it
	objectPtr->change(); //Syntactic sugar for (*objectPtr).change()
	std::cout << object.a << std::endl;
	
	return 0;
}
```


Pointer arithmetic
------------------

In addition to the dereference and addressof operators, pointers can be manipulated using arithmetic operators, such as increment and decrement. These behave similarly to other types, but with one important difference:

- **When a pointer is incremented or decremented, the value to increment or decrement by, is multiplied by the size of the datatype denoted by the pointer's type.**

The code example below demonstrates this behaviour:

```cpp
#include <iostream>

int main()
{
	bool*   boolPtr    = 0;
	int*    integerPtr = 0;
	double* doublePtr  = 0;
	int**   ptrPtr     = 0;
	int***  ptrPtrPtr  = 0;
	
	//This will increment the pointer by the size of a bool - 1 byte
	++boolPtr;
	
	//This will increment the pointer by the size of an int - 4 bytes
	++integerPtr;
	
	//This will increment the pointer by the size of a double - 8 bytes
	++doublePtr;
	
	//This will increment the pointer by the size of a pointer - 4 bytes (32-bit systems) or 8 bytes (64-bit systems)
	//NOTE that the type of the data on the bottom plane is irrelevant, the size is that of a *pointer*
	++ptrPtr;
	
	//This will increment the pointer by the size of a pointer - 4 bytes (32-bit systems) or 8 bytes (64-bit systems)
	//NOTE that the type of the data on the bottom plane is irrelevant, the size is that of a *pointer*
	++ptrPtrPtr;
	
	std::cout << boolPtr    << std::endl;  //Prints the memory address 1 (0x01)
	std::cout << integerPtr << std::endl;  //Prints the memory address 4 (0x04)
	std::cout << doublePtr  << std::endl;  //Prints the memory address 8 (0x08)
	
	//These both print 4 (0x04) for 32-bit targets, and 8 (0x08) for 64-bit targets
	std::cout << ptrPtr     << std::endl;
	std::cout << ptrPtrPtr  << std::endl;
	
	return 0;
}
```

**This behaviour applies to objects as well as primitive types.** In the case of an object, the size is determined by the memory layout for its class, as generated by the compiler. For any given class, this size may vary across compilers and target architectures.


Arrays in C and C++
-------------------

Unlike in a number of newer languages, arrays in C and C++ are not objects. In actuality, **arrays in C and C++ are pointers.**

The square-bracket syntax for accessing array members is in actuality a simple syntactic sugar for pointer arithmetic and a dereference, as demonstrated by the code example below:

```cpp
#include <iostream>

int main()
{
	//Allocates memory for 5 ints, which is 20 bytes (4 bytes per integer * 5)
	//The pointer will point to the start of the allocated block of memory
	int* intArray = new int[5];
	
	//These two statements are completely equivalent
	intArray[0]     = 7;
	*(intArray + 0) = 7;
	
	//As are these
	intArray[1]     = 8;
	*(intArray + 1) = 8;
	
	//And these
	intArray[2]     = 9;
	*(intArray + 2) = 9;
	
	//The special delete[] operator is used for deallocating arrays in C++, instead of the normal delete operator
	delete[] intArray;
	return 0;
}
```

As the pointer points to **the start of the allocated block of memory**, array index zero (which increments the pointer by zero, leaving its value unchanged), will access the first element in the array. It is for precisely this reason that arrays are zero-indexed in C and C++, a convention which is adopted by a number of other programming languages for the sake of consistency. 

Arrays can also be declared on the stack, **so long as the length is known at compile-time**. In this case, space is allocated on the stack for both the array data and the pointer to it. Because the array data is located on the stack, it is created and destroyed automatically when the stack frame is created and destroyed.

```cpp
#include <iostream>

int main()
{
	//Allocates memory for 5 ints, as well as memory for the pointer itself, both on the *stack*
	//The pointer will point to the start of the allocated block of memory
	int intArray[5];
	
	//These two statements are completely equivalent
	intArray[0]     = 7;
	*(intArray + 0) = 7;
	
	//As are these
	intArray[1]     = 8;
	*(intArray + 1) = 8;
	
	//And these
	intArray[2]     = 9;
	*(intArray + 2) = 9;
	
	//No need to free the array's memory, it is cleaned up automatically when the stack frame is destroyed
	return 0;
}
```


Pointer casting and arithmetic
------------------------------

As stated before, pointer arithmetic is influenced by the size of the data type denoted by the pointer's type. Accordingly, the results of pointer arithmetic can be manipulated through the use of typecasting.

Consider a modified version of the earlier example, where we utilise typecasting when performing pointer arithmetic:

```cpp
#include <iostream>

int main()
{
	bool*   boolPtr    = 0;
	int*    integerPtr = 0;
	double* doublePtr  = 0;
	int**   ptrPtr     = 0;
	int***  ptrPtrPtr  = 0;
	
	//This will increment the pointer by the size of an int - 4 bytes
	boolPtr = (bool*)((int*)boolPtr + 1);
	
	//This will increment the pointer by the size of a double - 8 bytes
	integerPtr = (int*)((double*)integerPtr + 1);
	
	//This will increment the pointer by the size of a bool - 1 byte
	doublePtr = (double*)((bool*)doublePtr + 1);
	
	//Underlying type is still irrelevant, will increment by the size of a pointer (4 bytes for 32-bit, 8 bytes for 64-bit)
	ptrPtr = (int**)((double**)ptrPtr + 1);
	
	//Underlying type is still irrelevant, will increment by the size of a pointer (4 bytes for 32-bit, 8 bytes for 64-bit)
	ptrPtrPtr = (int***)((double***)ptrPtrPtr + 1);
	
	std::cout << boolPtr    << std::endl;  //Prints the memory address 4 (0x04)
	std::cout << integerPtr << std::endl;  //Prints the memory address 8 (0x08)
	std::cout << doublePtr  << std::endl;  //Prints the memory address 1 (0x01)
	
	//These both print 4 (0x04) for 32-bit targets, and 8 (0x08) for 64-bit targets
	std::cout << ptrPtr     << std::endl;
	std::cout << ptrPtrPtr  << std::endl;
	
	return 0;
}
```

By typecasting a pointer, we are able to force it to increment (or decrement) based on multiples of the size of whatever datatype we cast it as being a pointer to. We can then typecast the result back to the original type, in order to store it in the original pointer variable.

This ability can be very useful when working with arrays of objects. Consider the code example below.

```cpp
#include <iostream>

class Dummy
{
	public:
		int a;
		int b;
};

int main()
{
	//Create an array of two instances of the Dummy class
	Dummy dummyArray[2];
	
	//Set the default values for the member variables of the instances
	dummyArray[0].a = 5;
	dummyArray[0].b = 7;
	dummyArray[1].a = 15;
	dummyArray[1].b = 17;
	
	//Create a pointer and make it point to a member variable of the first element
	int* num = &(dummyArray[0].b);
	
	//We can now manipulate the member variable of the first element by dereferencing the pointer
	*num = 6;
	
	//Using typecasting, increment the pointer by the size of the Dummy class
	//The pointer will now point to the same member variable, but of the *second* element
	num = (int*)((Dummy*)num + 1);
	
	//We can now manipulate the member variable of the second element by dereferencing the pointer
	*num = 99;
	
	//The output of these statements will be:
	//  5, 6
	//  15, 99
	std::cout << dummyArray[0].a << ", " << dummyArray[0].b << std::endl;
	std::cout << dummyArray[1].a << ", " << dummyArray[1].b << std::endl;
	
	return 0;
}
```

Clever use of typecasting allows us to exploit the contiguous memory layout of arrays, and manipulate the same member field in multiple elements, all through one pointer.

**This concludes Part One of our content on pointers and memory management. Next week's workshop will be Part Two.**
