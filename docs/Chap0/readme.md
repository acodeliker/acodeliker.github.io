# <font size=6>C++ main new features</font>
### 1.Attributes(_Attribute specifier sequence_)
&nbsp;&nbsp;&nbsp;&nbsp;one of the key features of modern C++ which allows the programmer to specify additional information to the compiler to enforce constraints(conditions), optimise certain pieces of code or do some specific code generation. 

&nbsp;&nbsp;&nbsp;&nbsp;In simple terms, an attribute acts as an annotation or a note to the compiler which provides additional information about the code for optimization purposes and enforcing certain conditions on it. 

&nbsp;&nbsp;&nbsp;&nbsp;Introduced in C++11, they have remained one of the best features of C++and are constantly being evolved with each new version of C++.

> #### Syntax:
```txt
// C++11
[[attribute-list]]

// C++17
[[using attribute-namespace:attribute-list]]

// C++20
[[contract-attribute-token contract-level identifier : expression]]
```
_Except for some specific ones, most of the attributes can be applied with variables, functions, classes, structures etc._

> #### Purpose of attributes:    

a. To enforce constraints on the code:

    Here constraint refers to a condition, that the arguments of a particular function must meet for its execution (precondition).

In previous versions of C++, the code for specifying constraints was written in this manner

```cpp
int f(int i)
{
    if (i > 0)
        return i;
    else
        return -1;

    // Code
}
```
It increases the readability of your code and avoids the clutter that is written inside the function for argument checking.
    
```cpp
int f(int i)[[expects:i > 0]]  //niubility
{
    // Code
}
```
b. To give additional information to the compiler for optimisation purposes:
        
    Compilers are very good at optimization 
    but compared to humans they still lag at some places and propose generalized code which is not very efficient. 
    
    This mainly happens due to the lack of additional information about the “problem” which humans have. 
    To reduce this problem to an extent C++ standard has introduced some new attributes 
    that allow specifying a little more to the compiler rather than the code statement itself. 
        
Once such example is that of likely.
```cpp
int f(int i)
{
    switch (i) {
    case 1:
        [[fallthrough]];
        [[likely]] case 2 : return 1;
    }
    return -1;
}
/* When the statement is preceded by likely compiler makes special optimizations with respect to that statement 
which improves the overall performance of the code. Some examples of such attributes are [carries_dependency], [likely], [unlikely]*/
```

c. Suppressing certain warnings and errors that programmer intended to have in his code:
    
    It happens rarely but sometimes the programmer intentionally tries to write a faulty code which gets detected by the compiler and is reported as an error or a warning.
    One such example is that of an unused variable which has been left in that state for a specific reason or of a switch statement 
    where the break statements are not put after some cases to give rise to fall-through conditions. 

In order to circumvent errors and warnings on such conditions,
C++ provides attributes such as [maybe_unused] and [fallthrough] that prevent the compiler from generating warnings or errors.

```cpp
#include <iostream>
#include <string>

int main()
{

    // Set debug mode in compiler or 'R'
    [[maybe_unused]] char mg_brk = 'D';

    // Compiler does not emit any warnings
    // or error on this unused variable
}
```

> #### List of standard attributes in C++

a. noreturn: 

Explanation
    indicates that the function does not return a value

Usage:

    [[noreturn]] void f();
    
    While looking at the code above, the question arises what is the point of having noreturn when the return type is actually void? 
    If a function has a void type, then it actually returns to the caller without a value 
    but if the case is such that the function never returns back to the caller (for example an infinite loop) 
    then adding a noreturn attribute gives hints to the compiler to optimise the code or generate better warnings.

Example:

```cpp    
#include <iostream>
#include <string>

[[noreturn]] void f()
{
    // Some code that does not return back the control to the caller
    // In this case the function returns back to the caller without a value
    // This is the reason why the
    // warning "noreturn' function does return' arises
}

void g()
{
    std::cout << "Code is intented to reach here";
}

int main()
{
    f();
    g();
}
```
Warning:

    main.cpp: In function 'void f()':
    main.cpp:8:1: warning: 'noreturn' function does return
    }
    ^

b. carries_dependency: 

Explanation:
    Indicates that dependency chain in release-consume std::memory_order propagates in and out of the function, which allows the compiler to skip unnecessary memory fence instructions.

Appear in two situations:
    
    1) it may apply to the parameter declarations of a function or lambda-expressions, 
    in which case it indicates that initialization of the parameter carries dependency into lvalue-to-rvalue conversion of that object.
    2) It may apply to the function declaration as a whole, in which case it indicates that the return value carries dependency to the evaluation of the function call expression.

Warning:

    This attribute must appear on the first declaration of a function or one of its parameters in any translation unit. If it is not used on the first declaration of a function or one of its parameters in another translation unit, the program is ill-formed; no diagnostic required.


c. More Attribute specifier sequence(since C++11) 

[Referrence](https://en.cppreference.com/w/cpp/language/attributes)

### 2.Uniform Initialization
&nbsp;&nbsp;&nbsp;&nbsp;A feature in C++ 11 that allows the usage of a consistent syntax to initialize variables and objects ranging from primitive type to aggregates.

&nbsp;&nbsp;&nbsp;&nbsp;In other words, it introduces brace-initialization that uses braces ({}) to enclose initializer values. 

> #### syntax

    type var_name{arg1, arg2, ....arg n}

```cpp
int main() {
    // 1 Since C++11, List-initialization, defined in the <initializer_list> header file, using a curly brace to solve everything.
    int value[]{1, 2, 3}; // ==  int value[] = {1,2,3}
    // std::initializer_list<T>A container array is associated with a(container)array<T,n> 
    // in which the elements are broken down by the compiler and passed to the function object one by one
    
    // 2 initializer_list<T> simple test
    vector<int> v{2, 3, 5};

    // 3 initializer_list<T> simple test
    /*************************************************************************************
    ** Compiler will form a initializer_list<string> for a curly brace below， 
    ** When the constructor of vector<string> is called by array<string,6>, 
    ** the compiler will find one that accepts the Overloaded constructor of initializer_list<string>
    ** All containers have such a constructor. In this constructor will use initializer_list<string> to initialize.
    **************************************************************************************/
    vector<string> city{"Berlin", "New York", "London", "Cairo","Tokyo", "Cologne"};

    /* CODING */
    
    return 0;
}

```

### 3.For loop over a range.

Used as a more readable equivalent 
to the traditional for loop operating over a range of values,
such as all elements in a container.
> #### Syntax

    attr(optional) for ( range_declaration : range_expression ) loop_statement	(until C++20)
    attr(optional) for ( init-statement(optional)range_declaration : range_expression )	loop_statement (since C++20)
    
attr
    -	any number of attributes

init-statement(C++20)	
    -	either
    • an expression statement (which may be a null statement ";")
    • a simple declaration, typically a declaration of a variable with initializer, but it may declare arbitrarily many variables or be a structured binding declaration
    Note that any init-statement must end with a semicolon, which is why it is often described informally as an expression or a declaration followed by a semicolon.

ange_declaration
	-	a declaration of a named variable, whose type is the type of the element of the sequence represented by range_expression, or a reference to that type. Often uses the auto specifier for automatic type deduction

range_expression
	-	any expression that represents a suitable sequence (either an array or an object for which begin and end member functions or free functions are defined, see below) or a braced-init-list.

loop_statement
	-	any statement, typically a compound statement, which is the body of the loop
range_declaration may be a structured binding declaration	(since C++17)
for (auto&& [first,second] : mymap) 	
    {
        // use first and second
    }
Explanation

### 4.Structured Binding
Binds the specified names to subobjects or elements of the initializer.
    Like a reference, a structured binding is an alias to an existing object. Unlike a reference, a structured binding does not have to be of a reference type.
    
> syntax：
    
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] = expression ;    (1) 	
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] { expression } ;  (2) 	
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] ( expression ) ;  (3) 
    
    attr	-	sequence of any number of attributes
    cv-auto	-	possibly cv-qualified type specifier auto, may also include storage-class-specifier static or thread_local; including volatile in cv-qualifiers is deprecated (since C++20)
    ref-qualifier	-	either & or &&
    identifier-list	-	list of comma-separated identifiers introduced by this declaration
    expression	-	an expression that does not have the comma operator at the top level (grammatically, an assignment-expression), and has either array or non-union class type. If expression refers to any of the names from identifier-list, the declaration is ill-formed.


