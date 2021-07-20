# <font size=6>C++ main new features since C++11</font>
### 1.Attributes(_Attribute specifier sequence_)
&nbsp;&nbsp;&nbsp;&nbsp;one of the key features of modern C++ which allows the programmer to specify additional information to the compiler to enforce constraints(conditions), optimise certain pieces of code or do some specific code generation. 

&nbsp;&nbsp;&nbsp;&nbsp;In simple terms, an attribute acts as an annotation or a note to the compiler which provides additional information about the code for optimization purposes and enforcing certain conditions on it. 

&nbsp;&nbsp;&nbsp;&nbsp;Introduced in C++11, they have remained one of the best features of C++ and are constantly being evolved with each new version of C++.

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

> #### Syntax

    type var_name{arg1, arg2, ....arg n}

Example:
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

### 3.Range-based for loop

&nbsp;&nbsp;&nbsp;&nbsp;Executes a for loop over a range.

&nbsp;&nbsp;&nbsp;&nbsp;Used as a more readable equivalent 
to the traditional for loop operating over a range of values,
such as all elements in a container.

> ##### Syntax

    attr(optional) for ( range_declaration : range_expression ) loop_statement	(until C++20)
    attr(optional) for ( init-statement(optional)range_declaration : range_expression )	loop_statement (since C++20)
    
    Description
        
    attr
    -	any number of attributes

    init-statement(C++20)	
    -	either
        • an expression statement (which may be a null statement ";")
        or
        • a simple declaration, typically a declaration of a variable with initializer, but it may declare arbitrarily many variables or be a structured binding declaration
        Note that any init-statement must end with a semicolon, which is why it is often described informally as an expression or a declaration followed by a semicolon.
    
    ange_declaration
	-	a declaration of a named variable, whose type is the type of the element of the sequence represented by range_expression, or a reference to that type. Often uses the auto specifier for automatic type deduction

    range_expression
	-	any expression that represents a suitable sequence (either an array or an object for which begin and end member functions or free functions are defined, see below) or a braced-init-list.

    loop_statement
	-	any statement, typically a compound statement, which is the body of the loop

range_declaration may be a structured binding declaration	(since C++17)   
```cpp
for (auto&& [first,second] : mymap) 	
    {
        // use first and second
    }
```

> ##### Explanation

The above syntax produces code equivalent to the following (__range, __begin and __end are for exposition only):
```cpp
{
    auto && __range = range_expression ;
        for (auto __begin = begin_expr, __end = end_expr; __begin != __end; ++__begin) {
        range_declaration = *__begin;
        loop_statement
    }
}
// (until C++17)

{
    auto && __range = range_expression ;
    auto __begin = begin_expr ;
    auto __end = end_expr ;
    for ( ; __begin != __end; ++__begin) {
        range_declaration = *__begin;
        loop_statement
    }
}
/* (since C++17)
(until C++20) */

{
    init-statement
    auto && __range = range_expression ;
    auto __begin = begin_expr ;
    auto __end = end_expr ;
    for ( ; __begin != __end; ++__begin) {
        range_declaration = *__begin;
        loop_statement
    }
}
```

Example:
```cpp
#include <iostream>
#include <vector>
 
int main() {
    std::vector<int> v = {0, 1, 2, 3, 4, 5};
 
    for (const int& i : v) // access by const reference
        std::cout << i << ' ';
    std::cout << '\n';
 
    for (auto i : v) // access by value, the type of i is int
        std::cout << i << ' ';
    std::cout << '\n';
 
    for (auto&& i : v) // access by forwarding reference, the type of i is int&
        std::cout << i << ' ';
    std::cout << '\n';
 
    const auto& cv = v;
 
    for (auto&& i : cv) // access by f-d reference, the type of i is const int&
        std::cout << i << ' ';
    std::cout << '\n';
 
    for (int n : {0, 1, 2, 3, 4, 5}) // the initializer may be a braced-init-list
        std::cout << n << ' ';
    std::cout << '\n';
 
    int a[] = {0, 1, 2, 3, 4, 5};
    for (int n : a) // the initializer may be an array
        std::cout << n << ' ';
    std::cout << '\n';
 
    for ([[maybe_unused]] int n : a)  
        std::cout << 1 << ' '; // the loop variable need not be used
    std::cout << '\n';
 
    for (auto n = v.size(); auto i : v) // the init-statement (C++20)
        std::cout << --n + i << ' ';
    std::cout << '\n';
 
}
```

[Original Address](https://en.cppreference.com/w/cpp/language/range-for)

### 4.Structured Binding Declaration
Binds the specified names to subobjects or elements of the initializer. (since C++17)
    
Like a reference, a structured binding is an alias to an existing object. Unlike a reference, a structured binding does not have to be of a reference type.
    
> ##### Syntax：
    
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] = expression ;    (1) 	
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] { expression } ;  (2) 	
    attr(optional) cv-auto ref-operator(optional) [ identifier-list ] ( expression ) ;  (3) 
    
    Description:

    attr	             -	sequence of any number of attributes
    cv-auto	          -	possibly `cv-qualified` type specifier auto, may also include storage-class-specifier static or thread_local; including volatile in cv-qualifiers is deprecated (since C++20)
    ref-qualifier	    -	either & or &&
    identifier-list	  -    list of comma-separated identifiers introduced by this declaration
    expression	       -	an expression that does not have the comma operator at the top level (grammatically, an assignment-expression), and has either array or non-union class type. If expression refers to any of the names from identifier-list, the declaration is ill-formed.

> ##### Scenes of usage
		
A structured binding declaration then performs the binding in one of three possible ways, depending on E ：
	
    Case 1: if E is an array type, then the names are bound to the array elements.
	Case 2: if E is a non-union class type and std::tuple_size<E> is a complete type with a member named value (regardless of the type or accessibility of such member), then the "tuple-like" binding protocol is used.
    Case 3: if E is a non-union class type but std::tuple_size<E> is not a complete type, then the names are bound to the accessible data members of E.
			
Each of the three cases is described in more detail below.
	
Each structured binding has a referenced type, defined in the description below. 
This type is the type returned by decltype when applied to an unparenthesized structured binding.

- Case 1: binding an array

        1. Each identifier in the identifier-list becomes the name of an lvalue that refers to the corresponding element of the array. 
        2. The number of identifiers must equal the number of array elements.
        3. The referenced type for each identifier is the array element type. Note that if the array type E is cv-qualified, so is its element type.

    Example:
    ```cpp
    #include <iostream>
    int main()
    {
        int a[2] = {1, 2};       // Assign a value to an array with two elements
        
        // structured-binding
        auto [x, y] = a;           // Assign the values ​​of the two elements of this array to x and y respectively

        auto& [xr, yr] = a;        // xr and yr is alias of a[0] and a[1]
        // Change it, a more complete structured binding expression should add [[maybe_unused]] const at the head
        
        std::cout << x << " " << y << std::endl;
        xr = 5;
        std::cout << a[0] << " " << a[1] << std::endl; 
        return 0;
    }
    ```

- Case2 and Case3 details see the [Refference Manual](https://en.cppreference.com/w/cpp/language/structured_binding)

### 5.Placeholder type specifiers

For variables, specifies that the type of the variable that is being declared will be automatically deduced from its initializer.(since C++11)
    
    For functions, specifies that the return type will be deduced from its return statements.(since C++14)
    For non-type template parameters, specifies that the type will be deduced from the argument.(since C++17)

> ##### Syntax

    [type-constraint(optional)] auto	            (1)	(since C++11)
    [type-constraint(optional)] decltype(auto)	  (2)	(since C++14)

    Description:
    type-constraint(C++20)	
    -	a concept name, optionally qualified, optionally followed by a template argument list enclosed in <>
        1) type is deduced using the rules for template argument deduction.
        2) type is decltype(expr), where expr is the initializer.
> ##### Usage

When using auto, the compiler determines the true type of the auto variable according to the context

Eg: int c = 0; auto d = c;

Note: When auto is used as the return value of a function, it can only be used to define a function, not to declare a function

Such as:

```cpp
#pragma once
class Test
{
public:
    auto TestWork(int a ,int b);
}; //The compilation fails;

// Write the implementation in the header file, it can be compiled and passed,

// Because the compiler can determine the true type of auto according to the return value of the function (similar to the Inline class member function
```

Another example:

```cpp
#include <iostream>
#include <utility>
 
template<class T, class U>
auto add(T t, U u) { return t + u; } // the return type is the type of operator+(T, U)
 
// perfect forwarding of a function call must use decltype(auto)
// in case the function it calls returns by reference
template<class F, class... Args>
decltype(auto) PerfectForward(F fun, Args&&... args) 
{ 
    // 
    return fun(std::forward<Args>(args)...); 
}
 
template<auto n> // C++17 auto parameter declaration
auto f() -> std::pair<decltype(n), decltype(n)> // auto can't deduce from brace-init-list
{
    return {n, n};
}
 
int main()
{
    auto a = 1 + 2;          // type of a is int
    auto b = add(1, 1.2);    // type of b is double
    static_assert(std::is_same_v<decltype(a), int>);
    static_assert(std::is_same_v<decltype(b), double>);
 
    auto c0 = a;             // type of c0 is int, holding a copy of a
    decltype(auto) c1 = a;   // type of c1 is int, holding a copy of a
    decltype(auto) c2 = (a); // type of c2 is int&, an alias of a
    std::cout << "a, before modification through c2 = " << a << '\n';
    ++c2;
    std::cout << "a,  after modification through c2 = " << a << '\n';
 
    auto [v, w] = f<0>(); //structured binding declaration
 
    auto d = {1, 2}; // OK: type of d is std::initializer_list<int>
    auto n = {5};    // OK: type of n is std::initializer_list<int>
//  auto e{1, 2};    // Error as of DR n3922, std::initializer_list<int> before
    auto m{5};       // OK: type of m is int as of DR n3922, initializer_list<int> before
//  decltype(auto) z = { 1, 2 } // Error: {1, 2} is not an expression
 
    // auto is commonly used for unnamed types such as the types of lambda expressions
    auto lambda = [](int x) { return x + 3; };
 
//  auto int x; // valid C++98, error as of C++11
//  auto x;     // valid C, error in C++
}
```

> ##### [Original Address](https://www.geeksforgeeks.org/lambda-expression-in-c/)

### 6.Lambda expression

C++ 11 introduced lambda expression to allow us write an inline function which can be used for short snippets of code that are not going to be reuse and not worth naming. 

> ##### Info

First look at an example which shows a simple lambda that is passed as the third argument to the std::sort() function given by ISO C++ Standard:

```cpp
#include <algorithm>
#include <cmath>

void abssort(float* x, unsigned n) {
    std::sort(x, x + n,        
        // Lambda expression begins        
        [](float a, float b) {
                
            return (std::abs(a) <std::abs(b));
            
        } // end of lambda expression
    );
}
```

The following figure can represent the syntax of Lambda expressions:

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/lambda.png)
<!-- ![_png](_media/images/lambda.png) -->

1. capture clause (Also known as the lambda-introducer in the C++ specification.)

2. parameter list Optional. (Also known as the lambda declarator)

3. mutable specification Optional.

4. exception-specification Optional.

5. trailing-return-type Optional.

6. lambda body

That corresponds to the following:

```txt
[Capture list](Parameter list) mutable(optional) Exception attribute -> return type {

// Function body
}


The above grammatical rules are well understood except for the things in the [capture list], except that the function name of the general function is omitted, and the return value is in the form of ->.
```

> ##### Syntax:

    In its simplest form lambda expression can be defined as follows:
    [ capture clause ] (parameters) -> return-type  
    {   
        definition of method   
    } 

Generally return-type in lambda expression are evaluated by compiler itself and we don’t need to specify that explicitly and -> return-type part can be ignored but in some complex case as in conditional statement, compiler can’t make out the return type and we need to specify that.

The so-called capture list can actually be understood as a type of parameter. By default, the internal function body of a lambda expression cannot use variables outside the function body. At this time, the capture list can be used to transfer external data.

> capture clause

Lambda introduces new variables in the C++14 function body. It can capture variables in the surrounding scope. Which variables can be captured, passed by value or referenced, it needs to be explained.

```txt
[]: Do not capture any variables by default;

[=]: Capture all variables by value by default;

[&]: Capture all variables by reference by default;

[x]: Only capture x by value, not other variables;

[&x]: Only capture x by reference, other variables are not captured;

[=, &x]: By default, all variables are captured by value, but x is an exception, captured by reference;

[&, x]: By default, all variables are captured by reference, but x is an exception, captured by value;

[this]: Capture the current object by reference (actually copy the pointer);

[*this]: Capture the current object by passing value;
```

For example, if you want to quote capture total and value capture factor, the following expressions can achieve the goal:

```txt
[&total, factor]

[factor, &total]

[&, factor]

[factor, &]

[=, &total]

[&total, =]
```

When the default capture is used, only the variables used in the lambda body will be captured.


When there is a default reference capture & in the capture list, no other captures can also have &. 
In the same way, with the default value capture=, there can be no other default value capture=. 
That is, an identifier or ``this`` cannot appear twice in the capture list. 

Look at the following example:

```cpp
struct S {void f(int i); };
void S::f(int i) {    
    [&, i]{}; // OK
    
    [&, &i]{}; // ERROR: i preceded by & when & is the default
    
    [=, this]{}; // ERROR: this when = is the default

    [=, *this]{ }; // OK: captures this by value. See below.
    
    [i, i]{}; // ERROR: i repeated
}
```

After std:c++17, ``this`` pointer can be captured by ``*this``. By value capture, it means that the entire closure (closure) is copied to the place where the lambda is called. This closure is an anonymous function object that encapsulates the lambda expression. 
The value capture of lambda is very useful in parallel or asynchronous operations. The corresponding code may be executed after the original object leaves the scope. Especially in a specific hardware architecture, it is not as good as NUMA.

    You may wonder what is the meaning of the square bracket at the beginning of the lambda expression? 
    In fact, this is a very important function of lambda expressions, that is, closures. 
    Here we first talk about the general principle of lambda expression: whenever you define a lambda expression, the compiler will automatically generate an anonymous class (this class of course overloads the () operator), we call it a closure type ( 
    closure type). 
    Then at runtime, this lambda expression will return an anonymous closure instance, which is actually an rvalue. 
    Therefore, the result of our lambda expression above is a closure. 
    One of the power of closures is that they can capture variables in their encapsulation scope by value or reference. The square brackets in front are used to define the capture mode and variables, and we call them lambda capture blocks.

In the above capture method, please note that it is best not to use the default capture of [=] and [&]. 
Dangling references are likely to occur, because reference capture does not extend the life cycle of the referenced variable:

```cpp
std::function<int(int)> add_x(int ​​x)
{
    return [&](int a) {return x + a; };
}
```

Because the parameter x is only a temporary variable, it is destroyed after the function is called, but the returned lambda expression refers to the variable, but when this expression is called, it refers to a garbage value, so it will produce meaningless results.

[more examples of lambda expression](https://docs.microsoft.com/en-us/cpp/cpp/examples-of-lambda-expressions)

> Value capture

Similar to parameter passing by value, the premise of value capture is that the variable can be copied. The difference is that the captured variable is copied when the lambda expression is created, rather than when it is called:

```cpp
void lambda_value_capture() {
    int value = 1;
        
    auto copy_value = [value] {
        return value;
    };
    
    value = 100;
    auto stored_value = copy_value(); 
    
    std::cout << "stored_value = "<< stored_value << std::endl; 
    // At this time, stored_value == 1, and value == 100.
    // Because copy_value saves a copy of value when it is created
}
```

> Reference capture

Similar to passing by reference, reference capture saves the reference, and the value will change:

```cpp
void lambda_reference_capture() {
    int value = 1;
    
    auto copy_value = [&value] {    
        return value;
    };
    value = 100;
    auto stored_value = copy_value();
    std::cout << "stored_value = "<< stored_value << std::endl;
    // At this time, stored_value == 100, value == 100.
    // because copy_value stores a reference
}
```

**Value capture** vs **reference capture**, especially multithreading:

    1. Reference capture can be used to modify external variables, but value capture cannot (mutable allows the copy to be modified, not the original value).

    2. Reference capture reflects the update of external parameters, but value capture does not.

    3. Reference capture introduces a lifetime dependency, but value capture does not, which is very important in asynchronous operations. In async lambda, if the reference captures a local variable, the lambda runs, and the local variable disappears, there will be an access violation problem. Refer to the Dangling references example above.

> ``this`` pointer

```cpp
// function_lambda_expression.cpp
// compile with: /EHsc /W4
#include <algorithm>
#include <iostream>
#include <vector>

using namespace std;

class Scale
{
public:
    // The constructor.
    explicit Scale(int scale) : _scale(scale) {}

    // Prints the product of each element in a vector object
    // and the scale value to the console.
    void ApplyScale(const vector<int>& v) const
    {
        for_each(v.begin(), v.end(), [=](int n) { cout << n * _scale << endl; });
    }

private:
    int _scale;
};

int main()
{
    vector<int> values;
    values.push_back(1);
    values.push_back(2);
    values.push_back(3);
    values.push_back(4);

    // Create a Scale object that scales elements by 3 and apply
    // it to the vector object. Does not modify the vector.
    Scale s(3);
    s.ApplyScale(values); // 3 6 9 12
}
```

To use lambda expressions in functions, you can use an explicit this pointer:

```cpp
// capture "this" by reference
void ApplyScale(const vector<int>& v) const
{
   for_each(v.begin(), v.end(),
      [this](int n) { cout << n * _scale << endl; });
}

// capture "this" by value (Visual Studio 2017 version 15.3 and later) c++17
void ApplyScale2(const vector<int>& v) const
{
   for_each(v.begin(), v.end(),
      [*this](int n) { cout << n * _scale << endl; });
}
```

You can also use the implicit this pointer:

```cpp
void ApplyScale(const vector<int>& v) const
{
    for_each(v.begin(), v.end(), 
        [=](int n) {cout << n * _scale << endl; });
}
```

There is a member method ApplyScale in this Scale class, which uses a lambda expression, which uses the data member _scale of the class. 
Use the default value method [=] to capture variables. 
You might think that this lambda expression also captures a copy of _scale, and the answer is wrong. 
Because the data member _scale is not visible to the lambda expression, it can be verified with the following code:

```cpp
//Cannot compile because _scale is not in the range captured by lambda
void ApplyScale(const vector<int>& v) const
{
    for_each(v.begin(), v.end(),
        [_scale](int n) {cout << n * _scale << endl; });
}
```

In the original code, each non-static method has a this pointer variable. Using the implicit this pointer, any member variable can be approached, so the lambda expression actually captures a copy of this pointer, so the original code is equivalent 
in:

```cpp
void ApplyScale(const vector<int>& v) const
{
   for_each(v.begin(), v.end(),
      [this](int n) { cout << n * this->_scale << endl; });
}
```

> ##### Usage

Various uses of lambda expression with standard function are given below :

```cpp

// C++ program to demonstrate lambda expression in C++
#include <bits/stdc++.h>
using namespace std;

// Function to print vector
void printVector(vector<int> v)
{
	// lambda expression to print vector
	for_each(v.begin(), v.end(), [](int i)
	{
		std::cout << i << " ";
	});
	cout << endl;
}

int main()
{
	vector<int> v {4, 1, 3, 5, 2, 3, 1, 7};

	printVector(v);

	// below snippet find first number greater than 4
	// find_if searches for an element for which
	// function(third argument) returns true
	vector<int>:: iterator p = find_if(v.begin(), v.end(), [](int i)
	{
		return i > 4;
	});
	cout << "First number greater than 4 is : " << *p << endl;


	// function to sort vector, lambda expression is for sorting in
	// non-decreasing order Compiler can make out return type as
	// bool, but shown here just for explanation
	sort(v.begin(), v.end(), [](const int& a, const int& b) -> bool
	{
		return a > b;
	});

	printVector(v);

	// function to count numbers greater than or equal to 5
	int count_5 = count_if(v.begin(), v.end(), [](int a)
	{
		return (a >= 5);
	});
	cout << "The number of elements greater than or equal to 5 is : "
		<< count_5 << endl;

	// function for removing duplicate element (after sorting all
	// duplicate comes together)
	p = unique(v.begin(), v.end(), [](int a, int b)
	{
		return a == b;
	});

	// resizing vector to make size equal to total different number
	v.resize(distance(v.begin(), p));
	printVector(v);

	// accumulate function accumulate the container on the basis of
	// function provided as third argument
	int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
	int f = accumulate(arr, arr + 10, 1, [](int i, int j)
	{
		return i * j;
	});

	cout << "Factorial of 10 is : " << f << endl;

	//	 We can also access function by storing this into variable
	auto square = [](int i)
	{
		return i * i;
	};

	cout << "Square of 5 is : " << square(5) << endl;
}
```

```txt
Output:

4 1 3 5 2 3 1 7 
First number greater than 4 is : 5
7 5 4 3 3 2 1 1 
The number of elements greater than or equal to 5 is : 2
7 5 4 3 2 1 
Factorial of 10 is : 3628800
Square of 5 is : 25
```

[Original Reference 0](https://docs.microsoft.com/en-us/cpp/cpp/lambda-expressions-in-cpp)

[Original Reference 1](https://zhuanlan.zhihu.com/p/143884880)

[Original Reference 2](https://www.geeksforgeeks.org/lambda-expression-in-c/)


### 7.Smart pointer

!> need to include header:&lt;memory&gt;

- auto_ptr

    1. At the end of the life cycle, automatically destroy the pointed memory space

    !> ps: Cannot point to a heap array (because the destructor of auto_ptr uses delete to delete pointers, not delete[])

    2. The constructor of auto_ptr is of explicit type, so it can only display initialization, such as:

    ```cpp
    auto_ptr<int> ap1(new int(1)); //Initialize correctly, create an ap1 class template object, make the pointer in the class template an int* type, and point to the address of 1.

    int* p = new int(1);

    auto_ptr<int> ap2(p); //initialization is correct

    // auto_ptr<int> ap3 = new int(2); //Error, cannot be initialized implicitly
    ```

    3. Provide the get() member function, which can be used to view the pointer address in the class. For example:

    ```cpp
    auto_ptr<int> ap(new int(1));

    cout<< ap.get()<<endl; //Print the address of the value 1: 0x6d2d18
        

    int *p =ap.get();

    cout<< *p<<endl; //Print the value 1
    ```

    4. A piece of heap space belongs to only one smart pointer object (because when multiple smart pointers pointing to the same address call the destructor, a bug will occur)

    5. When auto_ptr is copied or assigned, the address pointed to by its own pointer will be preempted, such as:

    ```cpp
    auto_ptr<int> p1(new int(1));

    auto_ptr<int> p2(new int(2));

    p1 = p2; //First delete the class member pointer of the p1 object, then assign the class member pointer of the p2 object to p1, and finally modify the p2 pointer address to NULL

    cout<<"p2 ="<<p2.get()<<endl; //Print: p2=0

    //cout<<*p2<<endl; //error, because p2=0
    ```

- shared_ptr 
    
    > #### Intro

    1. With reference counting mechanism, support multiple pointer objects to point to the same piece of memory (shared)

    2. Provide the swap() member function to exchange two objects of the same type, such as: 

        ```cpp

        shared_ptr<int> p1(new int(1));

        shared_ptr<int> p2(new int(2));

        p1.swap(p2); //After exchange p1=2,p2=1

        cout<< *p1 <<endl; //print 2

        cout<< *p2 <<endl; //print 1

        ```

    3. Provide the unique() member function to determine whether the address of the pointer is referenced by other pointers

    4. Provide the get() member function to get the address pointed to by the pointer object

    5. Provide the reset() member function, set the address of its own pointer object to NULL, and count the reference to -1 (when the count is 0, it will automatically delete the memory)

    6. Provide the use_count() member function, which can be used to view the number of reference counts, such as:

        ```cpp
        shared_ptr<int> sp1(new int(30)); //Count +1

        cout<<sp1.use_count()<<endl; //Print count: 1

        cout<<sp1.unique()<<endl; //Print: 1

        shared_ptr<int> sp2(sp1); //Count +1

        cout<<sp1.use_count()<<endl; //Print: 2

        cout<<sp1.unique()<<endl; //Because the sp1 pointer object is referenced by sp2, print: 0

        sp1.reset(); //Set the address of the sp1 pointer to NULL, count -1

        cout<<sp1.get()<<endl; //The address of the sp1 pointer is NULL, print: 0

        cout<<sp2.use_count()<<endl; //Print: 1

        cout<<sp2.unique()<<endl; //Since sp1 is released, only sp2 points to the address of 30, so print: 1
        ```

    > #### Usage

    ```cpp
    #include <iostream>
    #include <memory>

    using namespace std;

    class Test
    {
    public:
        int mvalue;
        Test(int i=0)
        {
                mvalue=i;
                cout<< "Test("<<mvalue<<")"<<endl;
        }

        ~Test()
        {
                cout<< "~Test("<<mvalue<<")"<<endl;
        }
    };

    int main()
    {  
        cout<<"*****begin*****"<<endl;

        shared_ptr<Test> p1(new Test(1));
        shared_ptr<Test> p2(p1);

        cout<<"*p1="<< p1->mvalue<<","<<"*p2="<<p2->mvalue<<endl;

        p1.reset();
        p2.reset();     

        cout<<"count:"<<p2.use_count()<<endl;

        cout<<"*****end*****"<<endl;
        return 0;
    }
    ```

    ```txt
    运行打印:
        *****begin*****
        Test(1)
        *p1=1, *p2=1
        ~Test(1)
        count:0
        *****end*****
    From the results, we can see that after we release both p1 and p2, since count=0, we automatically delete the Test pointer.

    ```

    Other smart pointers in STL (learned later, let's describe it in depth)

    [Original Address](https://cloud.tencent.com/developer/article/1099957)


- unique_ptr

    The use of unique_ptr tramples

        unique_ptr is an exclusive object, so it cannot be directly used as a parameter. What should I do?

    > As a parameter
    
    what if the function takes unique_ptr as a parameter?

    If you directly use unique_ptr as a parameter like the following, an error will definitely be reported, because it is not allowed to be copied:

    ```cpp
    #include<iostream>

    #include<memory>

    void test(std::unique_ptr<int> p)
    {
        *p = 10;
    }

    int main()
    {
        std::unique_ptr<int> up(new int(42));
    
        test(up);//Trying to pass in unique_ptr, compile error
    
        std::cout<<*up<<std::endl;
    
        return 0;

    }   
    ```
    
    The above code compilation will directly report an error. 

    Of course, we can pass ordinary pointers to the function, and use the get function to get raw pointers, such as:

    ```cpp
    #include<iostream>
    #include<memory>
    
    void test(int *p)
    {
        *p = 10;
    }
    int main()
    { 
        std::unique_ptr<int> up(new int(42));
    
        test(up.get());//Pass in a bare pointer as a parameter
    
        std::cout<<*up<<std::endl;//output 10
    
        return 0;
    }
    ```
    Or use a reference as a parameter:

    ```cpp
    #include<iostream>
    #include<memory>
    void test(std::unique_ptr<int> &p)
    {
        *p = 10;
    }

    int main() {
        std::unique_ptr<int> up(new int(42));
    
        test(up);
    
        std::cout<<*up<<std::endl;//output 10
    
        return 0;
    }
    ```

    Of course, if you don’t need to use it anymore, you can transfer it completely and hand over the object to the function you call for management. 
    
    Here you can use the move semantic:

    ```cpp
    #include<iostream>

    #include<memory>

    void test(std::unique_ptr<int> p)
    {       
        *p = 10;
    }

    int main(
    {
        std::unique_ptr<int> up(new int(42));
        
        test(std::move(up));
        //test(std::unique_ptr<int>(up.release())); //This way is also possible
        
        return 0;
    }
    ```

    [Original Address](https://zhuanlan.zhihu.com/p/91328039)


### 8.Final `override =default =delete 

> #### syntax

a. Default and delete basically work on these 5 functions,

    As shown below,

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/default_delete.png)

    1. constructor,

    2. Copy constructor,

    3. Move constructor (Zoo && ---Rvalue reference),

    4. Assignment function,

    5 kinds of member functions such as move assignment function

i. Because in C++, if you customize these 5 functions, the compiler will no longer generate default related functions for you. But if we add the `=default` keyword at the end, You can retrieve and use the default function generated by the compiler (explicit default: tell the compiler to generate the default version of the function even if you define it yourself)

ii. ``=delete`` is opposite to the above, =delete means not to use this function,
That is to say, this function has been deleted and cannot be used. Once others use it, an error will be reported (explicit deletion: tell the compiler not to generate the default default version of the function)

The purpose of introducing these two new features is to enhance the "control of class default functions", so that programmers can more accurately control the default version of the function

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/default_delete_1.png)

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/no-copy.png)

b. `override` is used to explicitly override the virtual function of the parent class. It is equivalent to telling the compiler that this function is an intention to rewrite the parent class virtual function, and let the compiler help to check it. Without this keyword, the compiler will not help you check

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/override.png)

c. Two new functions are added to `final`: 

(1), prohibit base classes from being inherited

(2), prohibit virtual functions from being rewritten;

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/final.png)


### 9.What's new in the STL

> Reference Wrapper

When the template function parameter is a generic type, it is impossible to deduce whether to pass by value or by reference. By default, the method of passing by value is used. In this case, we can use `std::ref` to explicitly specify the template function to be instantiated by reference.

```cpp  
#include <functional>
#include <iostream>
    
template <class T>
void foo(T arg)
{
    arg++;
}

int main()    
{
    int count = 3;
    foo(count); //At this time, the value is passed, the template is instantiated as foo(int), and the count value remains unchanged       
    std::cout << count << std::endl;
        
    foo(std::ref(count)); //At this time, the reference is passed, the template is instantiated as foo(int&), and the count value is increased by 1
    std::cout << count << std::endl;
}
```

> [Smart Pointers](Chap0/readme?id=_7smart-pointer)

> Functor

Four boost libraries are also incorporated into the stl library:

    function

    bind

    result_of

    mem_fn

Among them, function and bind have been introduced in the introduction to the boost library before. After the auto keyword is supported, it is easier to create a function through bind. We only need to use one sentence to create a functor of a member function.

```cpp
    #include <iostream>
    #include <functional>
    using namespace std;
    using namespace std::placeholders; 

    struct X
    {
        bool foo (int a) { cout<< a << endl; return false;}
    };

    int main()
    { 
        X x;

        auto func = bind(&X::foo, &x, _1);
        func(5);
    }
```

!> When using 'bind', you need to add the using of std::placeholders, otherwise the compilation will report a syntax error.

But I feel that bind is basically given seconds by lambda expressions. For the above example, the lambda expression is written as follows:

```cpp
    auto func = [&x](int a) { x.foo(a); };
    function<void (int)> func = [&x](int a) { x.foo(a); };
```

Because lambda expressions are syntactic sugar, they are more readable (it feels that the simplicity is almost close to anonymous functions in C#), there is no placeholders such as _1, _2, and the way the function is called is also explicit and direct 
Calling is more intuitive.

And result_of feels basically useless after the introduction of auto. It is much simpler to use auto directly.

> new additionally container

The main additions to the container are as follows:

    tuple
    
    array
    
    unordered_set和unordered_map

Among them, tuple and array are basically standardized by boost-related libraries, while unordered_set and unordered_map are hash table set and map to provide higher query performance. 
The usage method is similar to the original binary tree version, so I won't introduce it here.


> Regular expression

Boost's regex library is finally standardized. If you want to use string processing, you don't need to look for third-party regular expression libraries everywhere. 
However, currently VC does not support unescaping characters like C# (gcc can), and the regular expressions in the code are still very difficult to read. I hope MS can support raw string literal as soon as possible.


> Thread

Boost's thread library is also standardized, and the packaged_task similar to the .Net TPL library is also standardized. Since it has a lot to introduce, I will write an article to introduce it, so I won't talk about it here. it would be [there](Chap0/readme?id=_10std::thread)


> Time function

In fact, the C language standard library provides a time function, but it is extremely difficult to use. Now Boost's time function `chrono` has been standardized. Although it is still not as easy to use as .Net's TimeSpan, it is at least better than the standard C set. 
too much.

```cpp
    #include <iostream>
    #include <chrono>
    #include <ctime>
    using namespace std;

    int fibonacci(int n)
    {
        if (n < 3) return 1;
        return fibonacci(n-1) + fibonacci(n-2);
    }

    int main()
    {
        auto start = chrono::system_clock::now();
        int result = fibonacci(40);
        auto end = chrono::system_clock::now();

        int elapsed_seconds = chrono::duration_cast<chrono::milliseconds>
                                 (end-start).count();

        auto end_time = chrono::system_clock::to_time_t(end);

        std::cout << "result: " << result << endl
                 << "finished computation at " << std::ctime(&end_time)
                 << "elapsed time: " << elapsed_seconds << "ms\n";
    }
```

[Original Address](https://www.cnblogs.com/TianFang/archive/2013/01/25/2876400.html)

### 10. enum class

> #### Info

In standard C++, `enum` types are not type-safe. 

`enum` types are treated as integers, which allows comparisons between two different `enum` types. 
The only safety mechanism provided by C++03 is that an integer or an enumerated value cannot be implicitly converted to another enumerated type. 

In addition, the integer type and size used by the enumeration are defined by the implementation method and cannot be specified explicitly. 

Finally, the names of enumerations are all exposed in the general scope, so C++03 two different enumerations cannot have the same enumeration name.

(For example, enum Side{ Right, Left }; and enum Thing{ Wrong, Right }; cannot be used together.)


C++11 introduced a special "enumeration class", Strongly enumeration class, to avoid the above problems. 

> #### Syntax

```cpp
enum class Enumeration{ Val1, Val2, Val3 = 100, Val4 /* = 101 */,};
```

This type of enumeration is type safe. 

Enumeration cannot be implicitly converted to integers; nor can they be compared with integer values. 
(The expression Enumeration::Val4 == 101 will trigger a compile-time error).

> #### Usage

#include <iostream>
using namespace std;

```cpp
enum class Enumeration1
{
    Val1, // 0
    Val2, // 1
    Val3 = 100,
    Val4 /* = 101 */
};
```

// Specify the type
```cpp
int main(int argc, char** argv)
{
    Enumeration1 my=Enumeration1::Val3;
    cout<<static_cast<int>(my)<<endl;

    cout<<static_cast<double>(Enumeration2::val2)<<endl;
    return 0;
}

//enum class Enumeration2:long {val1,val2=100,val3}; // val2=100.000400 error
```

[Original Address](https://www.cnblogs.com/moodlxs/p/10174533.html)

### 11. std::thread

> #### Info

1. Create a thread

　　 It is relatively simple to create a thread. Use std thread to instantiate a thread object and then create it. Example:

```cpp
#include <iostream>
#include <thread>
using namespace std;

void t1()  /// Used to simulate thread execution
{
    for (int i = 0; i < 20; ++i)
    {
        cout << "t1111\n";
    }
}
void t2()
{
    for (int i = 0; i < 20; ++i)
    {
        cout << "t22222\n";
    }
}
int main()
{
    thread th1(t1);  // Instantiate a thread object th1, use the function t1 to construct, and then the thread starts to execute (t1())
    thread th2(t2);

    cout << "here is main\n\n";

    return 0;
}

```

But this example is problematic, because the thread starts executing after the thread is created, but the main thread main() does not stop, still continues to execute and then exits. At this time, the thread object is still joinable, and the thread still exists but points to it. 
The thread object has been destroyed, so an exception will be thrown.

So how to ensure that the child thread exits the main thread after it has finished executing?


2. ``thread::join()``

　　Using the join interface can solve the above problems. The role of join is to make the main thread wait until the execution of the sub-thread ends. Example:

```cpp
#include <iostream>
#include <thread>
using namespace std;

void t1()
{
    for (int i = 0; i < 20; ++i)
    {
        cout << "t1111\n";
    }
}
void t2()
{
    for (int i = 0; i < 20; ++i)
    {
        cout << "t22222\n";
    }
}
int main()
{
    thread th1(t1);
    thread th2(t2);
    
    th1.join(); //wait th1 run
    th2.join(); //wait th2 run

    cout << "here is main\n\n";

    return 0;
}
```

At this time, the child thread can be executed normally. At the same time, pay attention to the last output, which shows that main waits for the end of the child thread to continue execution.

It should be noted that the thread object is no longer joinable after the join is executed, so join can only be called once

3. `thread::detach()`

    The problem mentioned in (1.) can also be solved by using detach. Detach is used to separate the thread object so that the thread can execute independently, but because there is no thread object pointing to the thread, it loses control over it.  When the object is destroyed, the thread will continue to execute in the background, but when the main program exits, there is no guarantee that the thread can finish executing. If there is no good control mechanism or this kind of background thread is more important, it is best not to use detach and join.

```cpp
int main()
{
    thread th1(t1);
    thread th2(t2);
    
    th1.detach();
    th2.detach();

    cout << "here is main\n\n";

    return 0;
}
```

4. ``mutex``

    The header file is <mutex>. Mutex is used to ensure thread synchronization and prevent different threads from operating the same shared data at the same time.

```cpp
int cnt = 20;
mutex m;
void t1()
{
    while (cnt > 0)
    {    
        m.lock();
        
        if (cnt > 0)
        {
            --cnt;
            cout << cnt << endl;
        }

        m.unlock();
    }
}
void t2()
{
    while (cnt > 0)
    {
        m.lock();
        
        if (cnt > 0)
        {
            --cnt;
            cout << cnt << endl;
        }

        m.unlock();
    }
}
int main()
{
    
    thread th1(t1);
    thread th2(t2);
    
    th1.join();
    th2.join();

    return 0;
}
```

But it is not safe to use mutex. When a thread exits abnormally before unlocking, other blocked threads cannot continue.


5. ``std::lock_guard``

    The use of lock_guard is relatively safe. It is scope-based and can be self-unlocked. When the object is created, it will acquire a mutex lock like m.lock(). When the life cycle ends, it will automatically be destructed (unlock ), will not affect other threads because a thread exits abnormally. Example:

```cpp
int cnt = 20;
mutex m;
void t1()
{
    while (cnt > 0)
    {    
        lock_guard<mutex> lockGuard(m);
        if (cnt > 0)
        {
            --cnt;
            cout << cnt << endl;
        }
        
    }
}
void t2()
{
    while (cnt > 0)
    {
        lock_guard<mutex> lockGuard(m);
        if (cnt > 0)
        {
            --cnt;
            cout << cnt << endl;
        }
    
    }
}
```

[Original Address](https://www.cnblogs.com/whlook/p/6573659.html)


### 12. thread_local

> #### Info

Variables modified by `thread_local` have the following characteristics:

- Variables are generated when the thread is created (different compiler implementations are slightly different, but the variable must be constructed before the first use in the thread).

- The thread is destroyed when it ends (destructuring, using the destructor feature, the `thread_local` variable can sense thread destruction events).

- Each thread has its own copy of the variable.

- `Thread_local` can be used in conjunction with `static` or `extern`, which will affect the link properties of variables.


> #### Usage
The following code demonstrates the life cycle of the `thread_local` variable in the thread:

```cpp
// thread_local.cpp
#include <iostream>
#include <thread>

class A {
public:
  A() {
    std::cout << std::this_thread::get_id()
              << " " << __FUNCTION__
              << "(" << (void *)this << ")"
              << std::endl;
  }

  ~A() {
    std::cout << std::this_thread::get_id()
              << " " << __FUNCTION__
              << "(" << (void *)this << ")"
              << std::endl;
  }

  // 线程中，第一次使用前初始化
  void doSth() {
  }
};

thread_local A a;

int main() {
  a.doSth();
  std::thread t([]() {
    std::cout << "Thread: "
              << std::this_thread::get_id()
              << " entered" << std::endl;
    a.doSth();
  });

  t.join();

  return 0;
}
```

Run the code, get the result:

```
$> g++ -std=c++11 -o debug/tls.out ./thread_local.cpp
$> ./debug/tls.out
01 A(0xc00720)
Thread: 02 entered
02 A(0xc02ee0)
02 ~A(0xc02ee0)
01 ~A(0xc00720)
$>
```

The variable `a` keeps a copy in the `main` thread and `t` thread respectively. 

[Original Address](https://www.jianshu.com/p/8df45004bbcb)


### 13. Thread Synchronization primitive

> #### Info

There are some essential basic synchronization primitives of C++ thread, such as `std::mutex`, `std::lock_guard`, `std::condition_variable`, etc.

Briefly introduced `mutex` and `lock_guard` before in [std::thread](Chap0/readme?id=_11std::thread) 

Now take a look at `condition_variable`:

`condition_variable` is the notification mechanism between threads. The following examples will be used to explain:

![_png](https://cdn.jsdelivr.net/gh/acodeliker/acodeliker.github.io/docs/_media/images/condition_variable.png)


> #### Usage
Condition variables must be used with `mutex`.

First, create a new global variable `mutex` and `condition variable` to use in two threads.

In thread one, firstly use `unique_lock` to lock `mutex`, and then pass `lck` to the wait operation of `cv`. 
The Wait operation first unlocks the lock, and then waits. The thread blocks until other threads notify the thread. 
Thread two is the notify process, it calls the notify_one() method. If the thread is in the waiting phase at this time, it will be notified that the thread will wake up and then lock the mutex again. 

Then there are several possible results for this section of the program. 

1. Result 1 is the most anticipated result of the above process. When thread 1 is in the waiting state, thread 2 will notify and thread 1 will wake up. 

2. The second result is that thread two starts running first and sends a notify, and thread one has not yet reached the waiting state, then from the perspective of thread two, there is no thread waiting for the notify, then the notify will be lost. At this point, as soon as the thread has just run to the waiting stage, from this point of view, there is no notification from other threads, then the thread will always be in the hang state.

3. Another possible result is that after the thread runs and reaches the waiting state, when there is no notify, it wakes up by itself for some reason. At this time, the second thread starts to notify. This self-wakeup situation is not a bug, but must exist in the design. It is called a spurious wakeup, so it must be able to handle this situation when using it. 

It is difficult for programmers to list the many possible results, and this kind of uncertainty is the essence of concurrent programming. 
The order of each thread is originally unknown, so there are many possible execution results. 

Generally speaking, you don't need to pay attention to which results are possible, but to focus on which results are impossible. 
In the above example, the impossible result is that Waiting and Notify are printed on one line. 

In this case, only thread one has printed Waiting and has not yet wrapped. At this time, thread two also happened to print Notify, and then thread one or two wraps again. 
However, because the printing in the thread is protected by a lock, the printing of the text and the line feed are completed together, otherwise they will not be completed, so this situation is impossible.

[Original Address](https://developer.aliyun.com/article/584964)