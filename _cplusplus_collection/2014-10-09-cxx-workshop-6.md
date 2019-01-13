---
layout: default
title: C++ Workshop 6 - The Standard Template Library (STL)
description: "STL Container Classes"
permalink: cplusplusworkshop6.html
---

# C++ Workshop 6 - The Standard Template Library (STL)

STL Container Classes
=====================

There are a number of container classes included in the STL, designed to accommodate a variety of different purposes. [The full list is available here](http://www.cplusplus.com/reference/stl/). Below are some highlights:

- [`std::vector`](http://www.cplusplus.com/reference/vector/vector/) - a generic sequence of elements, similar to a Python list or a Java ArrayList
- [`std::set`](http://www.cplusplus.com/reference/set/set/) - a sequence of **unique** elements, that cannot contain duplicates
- [`std::stack`](http://www.cplusplus.com/reference/stack/stack/) - a stack data structure, which is Last In First Out (LIFO)
- [`std::queue`](http://www.cplusplus.com/reference/queue/queue/) - a queue data structure, which is First In First Out (FIFO)
- **Map containers** - a mapping from keys to values, similar to a Python dictionary:
  - [`std::map`](http://www.cplusplus.com/reference/map/map/) - is equivalent to a Java [TreeMap](http://docs.oracle.com/javase/7/docs/api/java/util/TreeMap.html)
  - [`std::unordered_map`](http://www.cplusplus.com/reference/unordered_map/unordered_map/), introduced in C++11, is equivalent to a Java [HashMap](http://docs.oracle.com/javase/7/docs/api/java/util/HashMap.html)

The containers covered in this workshop are `std::vector` and `std::map`.


Sequence container - the `std::vector` class
============================================

The `std::vector` class represents a sequence of items. Unlike an array, a `std::vector` can be of any size, and will resize automatically as items are added or removed. Items can be added to the end of a `std::vector` using the `push_back()` method, and accessed directly using the square brackets operator (`[]`).

The code listing below demonstrates usage of the `std::vector` class:

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main()
{
	vector<int> vec;
	vec.push_back(1);
	vec.push_back(2);
	vec.push_back(3);
	vec.push_back(4);
	vec.push_back(5);
	
	cout << "Element 0: " << vec[0] << endl;
	cout << "Element 1: " << vec[1] << endl;
	cout << "Element 2: " << vec[2] << endl;
	cout << "Element 3: " << vec[3] << endl;
	cout << "Element 4: " << vec[4] << endl;
	
	return 0;
}
```
**Listing 1: simple usage of the std::vector class**

When compiling code in C++11 mode (using the compiler flag **`-std=c++11`**), `std::vector` instances can also be initialised declaratively using [initializer lists](http://www.cplusplus.com/reference/initializer_list/initializer_list/), using the same syntax as for arrays declared on the stack:

```cpp
//Equivalent to the 5 push_back() calls in the example above
vector<int> vec = {1, 2, 3, 4, 5};
```


Associative container - the `std::map` class
============================================

The `std::map` and `std::unordered_map` classes represent mappings from keys to values. The `std::map` class utilises a binary search tree implementation, which sorts keys according to a strict weak ordering. The `std::unordered_map` class, introduced in C++11, utilises a hash table implementation, and preserves the insertion order of keys. **Usage of the two classes is the same.** Elements can be accessed directly using the square brackets operator (`[]`).

The code listing below demonstrates usage of the `std::map` class:

```cpp
#include <iostream>
#include <map>

using namespace std;

int main()
{
	map<string, string> dict;
	dict["a"] = "hello";
	dict["b"] = "world";
	dict["c"] = "!";
	
	cout << "Element a: " << dict["a"] << endl;
	cout << "Element b: " << dict["b"] << endl;
	cout << "Element c: " << dict["c"] << endl;
	
	return 0;
}
```
**Listing 2: simple usage of the std::map class**

When inserting elements into a `std::map`, the key and value are supplied in the form of a [`std::pair`](http://www.cplusplus.com/reference/utility/pair/) (a 2-tuple), which can be created using the `std::make_pair` template function:

```cpp
dict.insert( std::make_pair<string,string>("d", "!!!") );
```

A `std::pair` object is used instead of two arguments for reasons of compatibility. STL functions designed to work with container classes typically represent element values using a single argument, and using two arguments would require a specific version to accommodate the `std::map` and `std::unordered_map` container classes.

When compiling code in C++11 mode (using the compiler flag **`-std=c++11`**), `std::map` instances can be initialised declaratively using initializer lists, in the same manner as a `std::vector`. The initializer list represents a series of `std::pair` instances, like so:

{% raw  %}
```cpp
//Equivalent to the three assignment statements in the earlier example
map<string, string> dict = {{"a", "hello"}, {"b", "world"}, {"c", "!"}};
```
{% endraw %}

Iterators
=========

If we wanted to output all of the items in a `std::vector`, we could do so using a `for` loop with an index counter:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

for (int i = 0; i < someInts.size(); ++i) {
	cout << someInts[i] << endl;
}
```

This approach works well for a `std::vector`, but is not generalisable to other container classes. To facilitate a more general approach, the STL utilises a programming concept known as an **iterator**.

An iterator is an object that facilitates the traversal of a container, using a generic interface that remains consistent regardless of the underlying container. Iterators are similar to database cursors (used in SQL stored procedures) and are present either explicitly or implicitly in most modern programming languages (for language-specific examples, have a look at [the Wikipedia article on iterators](http://en.wikipedia.org/wiki/Iterator)).

The `for` loop from the code example above can be rewritten using an iterator, like so:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

//Split over multiple lines for readability
for
(
	vector<int>::iterator currItem = vec.begin();
	currItem != vec.end();
	++currItem
)
{
	cout << *currItem << endl;
}
```

The loop header for the updated `for` loop does the following:

- Creates an interator of type `vector<int>::iterator`, which is an iterator for a `vector<int>` container
- Sets the iterator to the return value of the container's `begin()` method, which returns an iterator to the first element in the container
- Loops while the iterator is not equal to the return value of the container's `end()` method, which returns an iterator that acts as a **sentinel value**, and refers to the position **immediately after the last element in the container**
- Advances the operator at the end of each loop iteration, using the overloaded increment operator (`++`)

The loop body accesses the current element in the container by using the overloaded dereference operator (`*`), which returns a **reference to the element** at the iterator's current position. The type of the reference is based on the type of the iterator's corresponding container. In the case of the `vector<int>::iterator` above, the type of the reference is `int&`.

We can traverse a `std::map` instance in the same manner:

{% raw %}
```cpp
map<string, string> dict = {{"a", "hello"}, {"b", "world"}, {"c", "!"}};

//Split over multiple lines for readability
for
(
	map<string, string>::iterator currItem = dict.begin();
	currItem != dict.end();
	++currItem
)
{
	cout << (*currItem).first << " -> " << currItem->second << endl;
}
```
{% endraw %}

Note that when a `map<string, string>::iterator` is dereferenced, the returned reference type is **`std::pair<string, string>&`**. We can access the first element of the pair (the key) using the `first` property, and the second element (the value) using the `second` property. When accessing member fields or methods of the element, we can use either a dereference (`*`) and the dot operator (`.`), as is used for accessing the key in the example above, or we can utilise the syntactic sugar of the arrow operator (`->`), as is used for accessing the value in the example above.


Common pitfall: mismatched iterator pairs
-----------------------------------------

Whenever iterators are used to represent a range of values (such as the entirety of a container, or a subset of its contents), that range will be represented by two iterators:

- A start iterator, which represents the position of the first element in the range
- An end iterator, which is used as a sentinel value, and represents the position immediately after the last element in the range

Both the start and end iterator must come from **the same container**. It is a common mistake to use the iterator returned by the `begin()` method of one container, and accidentally pair it with the iterator returned by the `end()` method of a different container:

```cpp
vector<int> firstVec  = {1, 2, 3, 4, 5};
vector<int> secondVec = {6, 7, 8, 9, 10};

//WILL CAUSE INCORRECT BEHAVIOUR AND / OR CRASH THE PROGRAM!
for
(
	//Start iterator from firstVec
	vector<int>::iterator currItem = firstVec.begin();
	
	//End iterator from secondVec
	currItem != secondVec.end();
	
	++currItem
)
{
	//...
}
```

The example above may cause any number of behaviours, including access to arbitrary memory locations that could result in unexpected values or memory access violation errors. In order to avoid such mistakes, the following rule must always be adhered to:

**IMPORTANT RULE: When using start and end iterator pairs, both iterators must always come from the *same* container!**


Iteration using the C++11 foreach loop
======================================

C++11 introduces a foreach loop construct, which follows the same syntax as a Java foreach loop:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

for (int& currElem : vec) {
	cout << currElem << endl;
}
```

The foreach loop is simply a syntactic sugar for traversing containers using iterators. The code in the example above is equivalent to the following:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

//The foreach loop still uses an iterator internally
for (vector<int>::iterator iter = vec.begin(); iter != vec.end(); ++iter)
{
	//The foreach loop creates a reference for us automatically
	int& currElem = *iter;
	
	cout << currElem << endl;
}
```

A foreach loop to iterate over a `std::map` container would be as follows:

{% raw %}
```cpp
map<string, string> dict = {{"a", "hello"}, {"b", "world"}, {"c", "!"}};

//Note that the first element in the pair is const - this is because the key is immutable
for (std::pair<const string,string>& currElem : dict) {
	cout << currElem.first << " -> " << currElem.second << endl;
}
```
{% endraw %}


Using the C++11 `auto` keyword
------------------------------

Instead of explicitly writing the reference type when declaring a foreach loop, it is easier to use the C++11 `auto` keyword, which will be substituted for the correct type at compile-time using type deduction:

{% raw %}
```cpp
vector<int> vec = {1, 2, 3, 4, 5};

//auto resolves to int&
for (auto currElem : vec) {
	cout << currElem << endl;
}

map<string, string> dict = {{"a", "hello"}, {"b", "world"}, {"c", "!"}};

//auto resolves to std::pair<const string,string>&
for (auto currElem : dict) {
	cout << currElem.first << " -> " << currElem.second << endl;
}
```
{% endraw %}

This has a number of benefits aside from succinctness. Because the type is deduced by the compiler, it will always match the correct one. This avoids issues caused by **the programmer accidentally specifying the wrong type**. Consider the example below:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

//Note that the type is int instead of int&
for (int currElem : vec) {
	cout << currElem << endl;
}
```

This results in code that is equivalent to the following:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

for (vector<int>::iterator iter = vec.begin(); iter != vec.end(); ++iter)
{
	//COPIES the value, instead of binding a reference to it
	int currElem = *iter;
	
	cout << currElem << endl;
}
```

Because the programmer forgot to specify a reference type, each element in the container will be copied needlessly during traversal. This also prevents the value from being modified inside the body of the loop, because **the copy will be modified instead of the original**.

**For these reasons, it is always recommended to use the `auto` keyword when performing traversal with a foreach loop, because it simplifies code entry and prevents a number of common mistakes.**


Inserting elements
==================

All STL container classes that support insertion of elements at arbitrary positions provide a method named `insert()`. (Examples of container classes that don't support `insert()` are `std::stack` and `std::queue`, which only support insertion at one position.)

There will typically be at least two overloads for the `insert()` method:

1. A version that takes a single value to insert into the container
2. A version that takes a start and end iterator, which inserts a range of values from another container

For sequence containers, such as `std::vector`, both overloads will take, as their first argument, an iterator representing the position to insert the new value(s). Associative containers, such as `std::map`, simply take the argument(s) representing the value(s) to insert, described in the list above.

**Note that the version that takes a start iterator and an end iterator, treats the end iterator as a sentinel value, representing the position *immediately after* the last element in the range.**

The example below demonstrates inserting elements into `std::vector` containers:

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main()
{
	vector<int> vec          = {1, 2, 3, 4, 5};
	vector<int> anotherVec   = {2, 4, 6, 8, 10};
	
	//Inserts the value 100 at index 2
	//New contents of vec: {1, 2, 100, 3, 4, 5}
	vec.insert( vec.begin() + 2, 100 );
	
	//Appends the entire contents of anotherVec
	//New contents of vec: {1, 2, 100, 3, 4, 5, 2, 4, 6, 8, 10}
	vec.insert( vec.end(), anotherVec.begin(), anotherVec.end() );
	
	//Duplicates the first three values of vec and inserts them at index 7
	//New contents of vec: {1, 2, 100, 3, 4, 5, 2, 4, 6, 8, 10}
	vec.insert( vec.begin() + 7, vec.begin(), vec.begin() + 3 );
	
	return 0;
}
```


Removing elements
=================

All STL container classes that support the `insert()` method also provide a method named `erase()` to remove existing elements.

There will typically be at least two overloads for the `erase()` method:

1. A version that takes a single iterator, representing the element to be removed
2. A version that takes a start and end iterator, representing a range of values to be removed

**As when inserting, note that the version that takes a start iterator and an end iterator, treats the end iterator as a sentinel value, representing the position *immediately after* the last element in the range.**

The example below demonstrates removing elements from `std::vector` containers:

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main()
{
	vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
	
	//Removes the element at index 2
	//New contents of vec: {1, 2, 4, 5, 6, 7, 8, 9, 10}
	vec.erase( vec.begin() + 2 );
	
	//Removes the range of elements from index 4 to index 6 (inclusive)
	//New contents of vec: {1, 2, 4, 5, 9, 10}
	vec.erase( vec.begin() + 4, vec.begin() + 7 );
	
	return 0;
}
```


STL algorithm functions
=======================

The STL header file [`<algorithm>`](http://www.cplusplus.com/reference/algorithm/) contains a number of useful, commonly used algorithms for working with containers. Among other things, these algorithms include functionality for:

- Sorting
- Search and replace
- Minimum / maximum value computation
- Arbitrary transformation

One of the most useful functions when working with containers is [`std::find`](http://www.cplusplus.com/reference/algorithm/find/), which locates the first instance of the specified value in a container, and returns an iterator to it. `std::find` takes a start and end iterator representing the range to search within, and returns either an iterator to the found element if successful, or else the end iterator for the range when the value could not be found.

The example below demonstrates use of the `std::find` function:

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
	vector<int> vec        = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
	vector<int> anotherVec = {2, 4, 6, 8, 10, 12, 14, 16, 18, 20};
	
	//Find the value 5 in vec and remove it
	vector<int>::iterator fivePos = std::find(vec.begin(), vec.end(), 5);
	if (fivePos != vec.end())
	{
		//New contents of vec: {1, 2, 3, 4, 6, 7, 8, 9, 10}
		vec.erase(fivePos);
	}
	
	//Find the value 10 in vec
	//Note the use of auto - this will resolve to vector<int>::iterator
	auto tenPos = std::find(vec.begin(), vec.end(), 10);
	if (tenPos != vec.end())
	{
		//Find the value 12 in anotherVec
		auto twelvePos = std::find(anotherVec.begin(), anotherVec.end(), 12);
		if (twelvePos != anotherVec.end())
		{
			//Insert all of the values in anotherVec, from 12 onwards, after 10 in vec
			//New contents of vec: {1, 2, 3, 4, 6, 7, 8, 9, 10, 12, 14, 16, 18, 20}
			vec.insert( tenPos + 1, twelvePos, anotherVec.end() );
		}
	}
	
	return 0;
}
```

The other function that is most commonly used when working with sequence containers is [`std::sort`](http://www.cplusplus.com/reference/algorithm/sort/), which sorts the elements in a range **in-place**. (Note that it is redundant to sort an associative container such as `std::map` or `std::set`, since those containers already sort elements automatically.)

The example below demonstrates use of the `std::sort` function:

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
	vector<int> vec = {1, 8, 55, 22, 6, 9, 3, 8, 10};
	
	//Sort a subset of the container
	//New contents of vec: {1, 8, 6, 9, 22, 55, 3, 8, 10}
	std::sort( vec.begin() + 2, vec.begin() + 6 );
	
	//Sort the entire contents of the container
	//New contents of vec: {1, 3, 6, 8, 8, 9, 10, 22, 55}
	std::sort( vec.begin(), vec.end() );
	
	return 0;
}
```


Iterators and templates
=======================

Consider the situation where you want to create a template function that will operate on any kind of STL container. The following code example demonstrates a template function that does so:

```cpp
template<typename T>
void allForOneAndOneForAll(T& items)
{
	for
	(
		//This line causes a compiler error
		T::iterator currItem = items.begin();
		
		currItem != items.end();
		++currItem
	)
	{
		//Set the value of every element to 1
		//(Note that the std::fill function in <algorithm> already provides this functionality)
		*currItem = 1;
	}
}
```

The statement `T::iterator currItem = items.begin()` will cause a compiler error. The error message produced by GCC 4.7 is as follows:

- *`error: need 'typename' before 'T:: iterator' because 'T' is a dependent scope`*

This is because `T::iterator` depends on the value of the template parameter `T`, and the compiler does not know if `T::iterator` is a member field or a type. As suggested by the error message, we can instruct the compiler to treat `T::iterator` as a type by prepending the `typename` keyword:

```cpp
template<typename T>
void allForOneAndOneForAll(T& items)
{
	for
	(
		//No longer causes a compiler error
		typename T::iterator currItem = items.begin();
		
		currItem != items.end();
		++currItem
	)
	{
		//Set the value of every element to 1
		//(Note that the std::fill function in <algorithm> already provides this functionality)
		*currItem = 1;
	}
}
```


C++11 solution: foreach and `auto`
----------------------------------

Note that the issue of templates and dependent scope is avoided entirely by simply using a C++11 foreach loop with the `auto` keyword, since the compiler generates the loop code to deal with the iterator, and resolves `auto` to the correct reference type, automatically:

```cpp
template<typename T>
void allForOneAndOneForAll(T& items)
{
	//Much simpler, and sidesteps the whole dependent scope issue entirely
	for (auto currItem : items) {
		currItem = 1;
	}
}
```


Iterators and `const`
=====================

Consider the situation in which a `const` reference to an STL container is being passed to a function:

```cpp
#include <iostream>
#include <vector>

using namespace std;

void printVector(const vector<int>& vec)
{
	for 
	(
		//This line causes a compiler error
		vector<int>::iterator currItem = vec.begin();
		
		currItem != vec.end();
		++currItem
	)
	{
		cout << *currItem << endl;
	}
}

int main()
{
	//When passing by const ref, we can pass stack variables...
	vector<int> numbers = {1,2,3,4,5};
	printVector(numbers);
	
	//...and temporary values, such as a vector constructed from an initializer list
	printVector({2,4,6,8,10});
	
	return 0;
}
```

The statement `vector<int>::iterator currItem = vec.begin()` will cause a compiler error. A shortened version of the error message produced by GCC 4.7 is as follows:

- *`error: conversion from 'std::vector<int>::const_iterator' to 'std::vector<int>::iterator' requested`*

The error message indicates that the return type of `vec.begin()` is `std::vector<int>::const_iterator` - a **const iterator**. This type is returned because `vec` is a reference to a `const vector<int>`.

Recall that when accessing the methods of a `const` object, only those methods that are themselves marked as `const` can be called. All STL containers have **two versions** of `begin()` and `end()` - a `const` version and a non-`const` version. The non-`const` versions return an iterator of type `iterator`, whilst the `const` versions return an iterator of type `const_iterator`. When available, **the compiler will automatically choose either the `const` or non-`const` version of a method, based on the `const`-ness of the object being accessed** (or the `const`-ness of the reference or pointer used to access the object.)

When traversing an STL container accessed through a `const` reference, it is necessary to use a `const_iterator`. `const` iterators function identically to ordinary non-`const` iterators, except that **a `const` reference is returned when the iterator is dereferenced**. This facilitates traversal of the container, whilst respecting the immutability of its contents.

To fix the compiler error caused by the earlier code example, a `const_iterator` simply needs to be utilised:

```cpp
#include <iostream>
#include <vector>

using namespace std;

void printVector(const vector<int>& vec)
{
	for 
	(
		//No longer causes a compiler error
		vector<int>::const_iterator currItem = vec.begin();
		
		currItem != vec.end();
		++currItem
	)
	{
		//Dereferencing results in a const int&, providing read-only access to elements
		cout << *currItem << endl;
	}
}

int main()
{
	//When passing by const ref, we can pass stack variables...
	vector<int> numbers = {1,2,3,4,5};
	printVector(numbers);
	
	//...and temporary values, such as a vector constructed from an initializer list
	printVector({2,4,6,8,10});
	
	return 0;
}
```


C++11 solution: foreach and `auto`
----------------------------------

Note that, as is the case with the issue of templates and dependent scope, the issue of `const` iterators is avoided entirely by simply using a C++11 foreach loop with the `auto` keyword. This is because `auto` will be correctly resolved to a `const` reference:

```cpp
#include <iostream>
#include <vector>

using namespace std;

void printVector(const vector<int>& vec)
{
	//Much simpler, and sidesteps the whole const_iterator issue entirely
	//auto resolves to const int&
	for (auto currItem : vec) {
		cout << currItem << endl;
	}
}

int main()
{
	//When passing by const ref, we can pass stack variables...
	vector<int> numbers = {1,2,3,4,5};
	printVector(numbers);
	
	//...and temporary values, such as a vector constructed from an initializer list
	printVector({2,4,6,8,10});
	
	return 0;
}
```


Limitation of foreach loops: reverse traversal
==============================================

In addition to the types `iterator` and `const_iterator`, STL container classes also support ([among a few other iterator types](http://www.cplusplus.com/reference/iterator/)) a type of iterator called [`reverse_iterator`](http://www.cplusplus.com/reference/iterator/reverse_iterator/), which is used to traverse ranges in reverse order. Reverse iterators are retrieved using the **`rbegin()`** and **`rend()`** methods, and can be used in the same way as ordinary iterators:

```cpp
vector<int> vec = {1, 2, 3, 4, 5};

//Split over multiple lines for readability
for
(
	vector<int>::reverse_iterator currItem = vec.rbegin();
	currItem != vec.rend();
	++currItem
)
{
	cout << *currItem << endl;
}
```

Reverse iterators also support a `const` version called `const_reverse_iterator`:

```cpp
//const vector, must use a const_iterator to traverse
const vector<int> vec = {1, 2, 3, 4, 5};

//Split over multiple lines for readability
for
(
	vector<int>::const_reverse_iterator currItem = vec.rbegin();
	currItem != vec.rend();
	++currItem
)
{
	cout << *currItem << endl;
}
```

**The iterator returned by a container's `rbegin()` method represents the position of the last element in the container, whilst the iterator returned by a container's `rend()` method is a sentinel value that represents the position *immediately before* the first element in the container.** Incrementing a reverse iterator is equivalent to decrementing an ordinary iterator, whilst decrementing a reverse iterator is equivalent to incrementing an ordinary iterator.

Reverse iterators are useful because they simply reverse the order of traversal, without modifying the contents of the container that is being traversed. This is particularly handy when accessing a container through a `const` ref, as the container is immutable, and would need to be copied in order to reverse the actual order of the contents.

Unfortunately, the **C++11 foreach loop is designed to work with ordinary iterators**, and contains no functionality to specify that reverse iterators should be used. There are three options for reverse traversal when using a foreach loop:

- Reverse the actual order of the contents of the container **in-place**, using [`std::reverse`](http://www.cplusplus.com/reference/algorithm/reverse/) (not available when the container is `const`.)
- Copy the contents of the container and reverse the copy, either using `std::reverse` or [`std::reverse_copy`](http://www.cplusplus.com/reference/algorithm/reverse_copy/).
- Utilise a wrapper class to generate an object representing the reversed range, using reverse iterators internally. Such a wrapper class could be [written by the programmer](http://stackoverflow.com/questions/8542591/c11-reverse-range-based-for-loop), or [provided by a third-party code library](http://www.boost.org/doc/libs/1_56_0/libs/range/doc/html/range/reference/adaptors/reference/reversed.html).
