---
layout: default
title: C++ Workshop 2 - Classes and Polymorphism
description: "Make files and build system"
permalink: cplusplusworkshop2.html
---

# C++ Workshop 2 - Classes and Polymorphism

Makefiles
=========


Build systems
-------------

As mentioned at the end of the previous workshop, the use of header files allows translation units (source code files) to be recompiled only as needed - that is, when the contents of the source file or any of the header files it depends upon change, but not every time the contents of other source files change. This greatly speeds up compilation, as the minimum number of files that need to be recompiled are identified, as opposed to recompiling an entire project.

This process is facilitated by a piece of software known as a **build system**. Build systems exist for a large variety of programming languages, and are an important tool in a programmer's toolchain. There are a number of build systems for C/C++, but we will focus on one of the oldest and most widely used, **GNU Make**.


Makefile syntax and processing
------------------------------

In order to build a project, GNU Make utilises a project description file, known as a "**makefile**". At their simplest, makefiles contain two pieces of information:

- The commands required to build the project from its source code; and
- The dependencies between source files and header files

Makefiles are comprised of a series of **rules**, which take the following syntax:

```
RULE_NAME: DEPENDENCY1 DEPENDENCY2 DEPENDENCY3
	COMPILATION_COMMAND
	MORE_COMPILATION_COMMANDS
```
**Listing 1: general syntax of a makefile rule**

Rules can have any name, but are usually the name of the output file they produce. The dependencies of a rule can be either the names of other rules, or the names of files.

Listing 2 depicts an example makefile for the three source files from the end of the previous workshop (`utility.h`, `utility.cpp`, and `main.cpp`). Note that the Windows-specific executable extension of `.exe` is utilised simply because it makes it clear that we are referring to the output executable. Under other operating systems, the executable file will be extensionless.

```
# Rule to build the executable by linking the object files
test.exe: main.o utility.o
	g++ -o test.exe main.o utility.o

# Rule to compile utility.cpp into the object file utility.o
utility.o: utility.cpp utility.h
	g++ -c -o utility.o utility.cpp

# Rule to compile main.cpp into the object file main.o
main.o: main.cpp utility.h
	g++ -c -o main.o main.cpp
```
**Listing 2: makefile for the project from the end of the previous workshop**

To build the project, place the makefile in the same directory as the source files, with the extensionless filename `makefile`, and run the following command (under Windows, depending on your MinGW configuration, you may need to use `mingw32-make` instead):

```
make
```

This will invoke GNU Make. By default, Make will look in the current directory for a makefile with the filename `makefile`. You can specify a specific filename using the `-f` argument:

```
make -f FILENAME
```

Once Make has read the contents of the makefile, it will then process the first rule listed in the file. Rules are processed using the following steps:

- For each of the rule's dependencies, Make will check if it is the name of another rule. If it is, that rule will be processed. Otherwise, Make will assume the dependency is a filename, and will check if the file has been updated.
- If any of the dependencies has changed, Make will execute the commands listed in the body of the rule. The rule is then considered to have "changed", from the perspective of any other rules that list it as a dependency.
- If none of the dependencies have changed, Make will take no action for the rule. The rule will not be considered changed.

The first time `make` is run, it should execute the commands to compile all of the source files for the project. Subsequent invocations will check which files have changed when processing rule dependencies, and only recompile the source files that require it, linking any newly generated object files with the other unchanged object files to output the executable.


Non-file rules
--------------

As stated before, rules do not have to have the names of files. Makefiles commonly include a rule named `clean`, which has no dependencies and contains the commands to remove any generated output files, leaving only the original source files remaining. As an example, Listing 3 shows the makefile from Listing 2 with a `clean` rule added (once again, note that the `.exe` extension would only be present under Windows, as executable files are extensionless under other platforms):

```
# Rule to build the executable by linking the object files
test.exe: main.o utility.o
	g++ -o test.exe main.o utility.o

# Rule to compile utility.cpp into the object file utility.o
utility.o: utility.cpp utility.h
	g++ -c -o utility.o utility.cpp

# Rule to compile main.cpp into the object file main.o
main.o: main.cpp utility.h
	g++ -c -o main.o main.cpp

# Rule to remove all generated object files and the executable file
clean:
	rm *.o
	rm *.exe
```
**Listing 3: makefile from Listing 2 with a "clean" rule added**

To run the `clean` rule, invoke GNU Make like so:

```
make clean
```

The second argument to `make` specifies a rule to process instead of simply defaulting to the first rule in the file. Any rule can be processed directly via this method.


GNU Automake and Autoconf
-------------------------

Although we will not be using them ourselves, it is useful to be aware of GNU Automake and Autoconf, as they are used by a large number of open source C/C++ projects. These pieces of software represent another layer of build system complexity on top of GNU make, and allow programmers to specify additional configuration options for a project's build. When building a project that uses Automake and Autoconf, the first step is to run the configuration script (note that under Windows, the MSYS component of MinGW must be installed in order to run the configuration script through the `bash` shell):

```
./configure
```

The configuration script will analyse the host operating system and environment, and then **generate the makefile for the project**. The project can then be built using GNU Make with the generated makefile. Accordingly, the standard sequence of commands for building most open source C/C++ projects from their source code is as follows:

```
./configure
make
```

We will cover building and using open source C++ libraries in further detail in subsequent workshops.


Classes in C++
==============

This section assumes the reader is familiar with classes and object-oriented programming principles in other languages. As such, we focus primarily on the syntactic differences between classes in C++ and other languages, using Java for comparisons.


General syntax
--------------

Consider the following Java code for a class:

**MyClass.java**
```java
class MyClass
{
	private int num = 1;
	
	public MyClass() {
		//Constructor stuff
	}
	
	public void someMethod() {
		//Do stuff
	}
	
	private void secretMethod() {
		//Do stuff
	}
}
```

In C++, classes are of course split into matching header and source files. The equivalent class written in C++ is as follows:

**MyClass.h**
```cpp
class MyClass
{
	private:
		
		int num;
	
	public:
		
		MyClass();
		
		void someMethod();
	
	private:
		
		void secretMethod();
};
```

**MyClass.cpp**
```cpp
#include "MyClass.h"

MyClass::MyClass()
{
	num = 1;
	
	//Constructor stuff
}

void MyClass::someMethod() {
	//Do stuff
}

void MyClass::secretMethod() {
	//Do stuff
}
```

Important differences to note:

- The class declaration requires a semicolon at the end.
- Method implementations are not written inside the declaration. Instead, they are specified as belonging to the scope of the class using the scoping operator (`::`). **Note that method implementations can in fact be written inside the declaration if the class exists only in a single source file and has no header. If implementations are included in a header, you will get compiler errors.**
- Member variable values cannot be specified in the class declaration. They must be assigned in the constructor.
- Individual member variables and methods are not marked with a visibility specifier keyword (`public`, `protected`, `private`). Instead, these keywords are used as labels, and apply to all member variables and methods that appear after them, prior to the next keyword. Note that any visibility specifier keyword can be used as many times as desired - there is nothing restricting the programmer to utilising a single "`public` section" or "`private` section", for instance.
- Visibility specifier keywords are used only in the class declaration; they are not repeated in the definition code.


Static
------

The syntax for declaring static member variables and methods in a Java class is as follows:

**MyStaticClass.java**
```java
class MyStaticClass
{
	static public int num = 1;
	
	static public void staticMethod() {
		//Do stuff
	}
}
```

The equivalent in C++ is:

**MyStaticClass.h**
```cpp
class MyStaticClass
{
	public:
		
		static int num;
		static void staticMethod();
};
```

**MyStaticClass.cpp**
```cpp
#include "MyStaticClass.h"

void MyStaticClass::staticMethod() {
	//Do stuff
}

//Even if we don't want to include the " = 1" part, the rest of the following line is still required or we will get a compiler error!
int MyStaticClass::num = 1;
```

Important differences to note:

- Static member variables and methods **are marked** with the `static` keyword.
- The `static` keyword is  used only in the class declaration; it is not repeated in the definition code.
- **Regardless of whether or not default values are specified, static member variables must have a "definition" in the source file.** For example, if we wished to omit a default value for the member variable `num`, we would still need to include the following in **MyStaticClass.cpp**:
  
  `int MyStaticClass::num;`

As stated in the previous workshop, static member variables and methods are accessed using the scoping operator (`::`), like so:

```cpp
//Access static member variable
int anotherNumber = MyStaticClass::num;

//Call static method
MyStaticClass::staticMethod();
```


Inheritance
-----------

In Java and similar languages, the `extends` keyword is used to specify inheritance, whilst the `implements` keyword is used to specify implementing an interface:

```java
class DerivedClass extends BaseClass, implements SomeInterface
{
	//...
}

```

In C++, a colon is used instead:

```cpp
class DerivedClass : public BaseClass, public SomeInterface
{
	//...
};

```

Note that the interface class is specified in the same manner as the superclass. **There is no distinction between superclasses and interfaces in C++ - they are both simply treated as superclasses.** This is because C++ supports multiple inheritance. The distinction between interfaces and superclasses is only relevant in other programming languages (such as Java) that permit inheriting from only a single superclass, but implementing any number of interfaces.

Also note the use of the `public` keyword in the listing above. C++ supports three types of inheritance:

- **Public inheritance**: all of the member fields and methods from the parent class(es) retain their existing visibility specifiers. **This is the only type of inheritance supported by other programming languages, and is the one you will want to use almost all of the time.**
- *Protected inheritance*: all of the `public` member fields and methods from the parent class(es) become `protected`.
- *Private inheritance*: all of the `public` and `protected` member fields and methods from the parent class(es) become `private`. **Note that unless explicitly specified, C++ will default to private inheritance. You need to specify the `public` keyword for each superclass in order to use public inheritance for that class.**

As stated above, C++ makes no distinction between superclasses and interfaces. In fact, **C++ has no explicit concept of interfaces at all**. "Interfaces", as defined by other programming languages, are simply abstract base classes that consist entirely of abstract methods. Abstract methods are discussed in the section that follows.


Abstract methods
----------------

An **abstract class** is a class that contains one or more **abstract methods**. Abstract methods are methods for which only a method prototype is specified in the base class, and for which an implementation must be provided by a subclass. Accordingly, abstract classes cannot be instantiated. Only subclasses that provide implementations for the abstract methods, known as **concrete classes**, can be instantiated. (Any subclass which does not provide implementations for all abstract methods is itself considered an abstract class, and follows the same restrictions regarding instantiation as the superclass.)

In Java, an abstract class is written as follows:

**AbstractBaseClass.java**
```java
abstract class AbstractBaseClass
{
	public void nonAbstractMethod() {
		//Do stuff
	}
	
	abstract public void abstractMethod();
}
```

The equivalent class is written in C++ as:

**AbstractBaseClass.h**
```cpp
class AbstractBaseClass
{
	public:
		void nonAbstractMethod();
		
		virtual void abstractMethod() = 0;
};
```

**AbstractBaseClass.cpp**
```cpp
#include "AbstractBaseClass.h"

void AbstractBaseClass::nonAbstractMethod() {
	//Do stuff
}
```

Important differences to note:

- The class itself is not marked as `abstract` in C++. The compiler knows it is an abstract class because it contains one or more abstract methods.
- **There is no `abstract` keyword in C++**. Instead, abstract methods are marked using the `virtual` keyword (discussed below) and the suffix `= 0`.

C++ documentation refers to abstract methods as "**pure virtual**" methods. This terminology is specific to C++ (the more general "abstract" is used in most other programming languages) and is a reference to the `virtual` keyword. The meaning of the this keyword is discussed in the section that follows.


Polymorphism and the `virtual` keyword
--------------------------------------

To understand the `virtual` keyword, it is necessary to understand the underlying mechanism by which polymorphism operates in C++ (as well as numerous other modern programming languages.)

C++ compilers utilise the memory layout of a class to determine the memory offsets for each of its methods. As the memory layout is known at compile time, these offsets can be hardcoded during the compilation process. This makes method calls faster at runtime, but lacks the flexibility required for polymorphism. Without any additional mechanism in place, a method invocation will always call the version of the method that is available in the class specified by the type of our pointer ("reference" in Java.) As an example, consider the code below:

```cpp
class BaseClass
{
	public:
		void someMethod();
};

class DerivedClass : public BaseClass
{
	public:
		void someMethod();
};

//...

int main()
{
	//Ignore the asterisk for now, pointer syntax will be covered in the next workshop
	BaseClass* instance = new DerivedClass();
	
	//This will call BaseClass::someMethod
	instance->someMethod();
	
	//...
}
```

Because the type of our pointer is `BaseClass`, the compiler will hardcode the memory offset for `BaseClass::someMethod` in the machine code generated for the method call. The actual class of the object located at the memory address contained in the pointer is irrelevant.

To facilitate polymorphism, an extra level of indirection is introduced, in the form of a [virtual method table](http://en.wikipedia.org/wiki/Virtual_method_table), also known as a **vtable**. The vtable contains one entry for each method that we wish to enable polymorphic behaviour for. Each entry contains the memory location for the version of the method that should be used. The location of the vtable entries are the same in both the base class and the derived class, which allows the compiler to hardcode the memory offset for a method's vtable entry instead of the offset for the method itself. The value of the entry is then read at runtime, and the retrieved memory address is used to call the correct version of the method.

Because vtables introduce additional memory overhead and runtime cost, C++ does not enable polymorphic behaviour for class methods by default. **Only methods marked with the `virtual` keyword will be polymorphic.** This allows non-virtual methods to avoid the runtime speed cost of a vtable lookup, and also allows classes with no virtual methods defined to avoid the memory cost of including a vtable.

The following listing demonstrates the use of virtual and non-virtual methods:

```cpp
class BaseClass
{
	public:
		virtual void someMethod();
		void anotherMethod();
};

class DerivedClass : public BaseClass
{
	public:
		void someMethod();
		void anotherMethod();
};

//...

int main()
{
	//Ignore the asterisk for now, pointer syntax will be covered in the next workshop
	BaseClass* instance = new DerivedClass();
	
	//This will now call DerivedClass::someMethod, as it is virtual
	instance->someMethod();
	
	//This will call BaseClass::anotherMethod, as it is not virtual
	instance->anotherMethod();
	
	//...
}
```

Note that `someMethod` is not marked as `virtual` in `DerivedClass`. Marking the method as virtual in the derived class is optional - **once a method has been declared as virtual in the base class, it is virtual in all derived classes**. This is because the vtable entry for that method already exists, and cannot be removed without breaking the polymorphic behaviour of the derived class.

Some languages, including Java, C#, and PHP, enable mandatory polymorphism for all methods of all classes. In these languages, all classes contain vtables with entries for all of their non-static methods, whether the programmer intends to utilise polymorphism or not.

It is important to note that the requirement for marking methods as `virtual` to enable polymorphic behaviour is irrespective of whether the code calling the polymorphic method is inside the class itself or is external to it. The following example program illustrates the need to carefully consider whether or not methods need to be polymorphic:

```cpp
#include <iostream>

class A
{
	public:
		void func()
		{
			//ALWAYS calls A::otherFunc
			this->otherFunc();
		}
		
		void otherFunc() {
			std::cout << "A::otherFunc" << std::endl;
		}
};

class B : public A
{
	public:
		void yetAnotherFunc()
		{
			//Calls either B::otherFunc or C::otherFunc; is polymorphic
			this->otherFunc();
		}
		
		virtual void otherFunc() {
			std::cout << "B::otherFunc" << std::endl;
		}
};

class C : public B
{
	public:
		void otherFunc() {
			std::cout << "C::otherFunc" << std::endl;
		}
};

int main()
{
	A a;
	B b;
	C c;
	
	//Outputs "A::otherFunc"
	a.func();
	
	//Outputs "A::otherFunc"
	b.func();
	
	//Outputs "A::otherFunc"
	c.func();
	
	//Outputs "B::otherFunc"
	b.yetAnotherFunc();
	
	//Outputs "C::otherFunc"
	c.yetAnotherFunc();
	
	return 0;
}
```

Even though `otherFunc` is later declared `virtual` in `B` and becomes polymorphic for `B` and all of its subclasses, all code in `A` will remain hardcoded to use the version of the method defined in `A`. This highlights the importance of marking methods as `virtual` if you believe their functionality might ever need to be overriden in a subclass, as failing to mark them as such will make subsequent replacement of their behaviour through polymorphism impossible.
