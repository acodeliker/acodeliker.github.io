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

### 13. atomic operation

To use atomic operations, we need to reference a new header file `<atomic>` in C++11. 

In this header file, a class template `struct atomic` is defined to represent atomic data types. The GNU implementation (/usr/include/c++/4.8.3/atomic) is as follows:

```cpp
template<typename _Tp>
struct atomic
{
private:
   _Tp _M_i;
public:
    atomic() noexcept = default;
    ~atomic() noexcept = default;
    atomic(const atomic&) = delete;                //删除了拷贝构造
    atomic& operator=(const atomic&) = delete;       
    atomic& operator=(const atomic&) volatile = delete;   //删除了 operator=
    constexpr atomic(_Tp __i) noexcept : _M_i(__i) { }
    operator _Tp() const noexcept
    {
          return load();
    }
 
    operator _Tp() const volatile noexcept
    {
          return load();
    }
 
    _Tp operator=(_Tp __i) noexcept
    {
        store(__i);
        return __i;
    }
 
    ...
};
```

The `atomic` template also implements operator-overloading (see the atomic header file for the complete class structure), so you can use atomic data types like built-in data types (c++ guarantees that these operations are atomic operations) 
. 
Corresponding to the built-in data types, atomic data types have a corresponding type, which can be summarized as follows:

```cpp
std::atomic_char	std::atomic<char>
std::atomic_schar	std::atomic<signed char>
std::atomic_uchar	std::atomic<unsigned char>
std::atomic_short	std::atomic<short>
std::atomic_ushort	std::atomic<unsigned short>
std::atomic_int	std::atomic<int>
std::atomic_uint	std::atomic<unsigned int>
std::atomic_long	std::atomic<long>
std::atomic_ulong	std::atomic<unsigned long>
std::atomic_llong	std::atomic<long long>
std::atomic_ullong	std::atomic<unsigned long long>
// 更多的请见：http://en.cppreference.com/w/cpp/atomic/atomic
```

[Original Address](https://www.cnblogs.com/ittinybird/p/4830834.html)

### 14. std::function/bind

> #### Info

Although all the callable objects in C++ have a relatively uniform operation form, the definition methods are varied, which leads to the use of a unified way to save the callable object or transfer the callable object, it will be very cumbersome. 

C++11 provides `std::function` and `std::bind` to unify various operations of callable objects.

Different types may have the same calling form, such as:

```cpp
// common function
int add(int a, int b){return a+b;} 

// lambda expression
auto mod = [](int a, int b){ return a % b;}

// Fuctor
struct divide{
    int operator()(int denominator, int divisor){
        return denominator/divisor;
    }
};
```

Although the above three callable objects are of different types, they share a calling form:

```cpp
    int(int ,int)
```

`std::function` can store the types above, like this:

```cpp
    std::function<int(int ,int)>  a = add; 
    std::function<int(int ,int)>  b = mod ; 
    std::function<int(int ,int)>  c = divide(); 
```


> 1. `std::function`:

- `std::function` is a callable object wrapper. It is a class template that can hold all callable objects except class member function pointers. It can handle functions, function objects, and function pointers in a unified way, and allows saving and delay their execution.

- Definition format: `std::function`<function type>.

- `std::function` can take the place of the function pointer, because it can delay the execution of the function and is especially suitable for use as a callback function. It is more flexible and convenient than ordinary function pointers.

> 2. `std::bind`

The `std::bind` function can be regarded as a universal function adapter. It accepts a callable object and generates a new callable object to "adapt" to the parameter list of the original object.


`std::bind` binds the callable object and its parameters together, and the result of binding can be saved using `std::function`. 
`std::bind` mainly has the following two functions:

- Bind the callable object and its parameters into a Functor;

- Only bind some parameters, reduce the parameters passed in by the callable object.

> #### Usage

> 2.1 Bind common functions

```cpp
double my_divide (double x, double y) {return x/y;}
auto fn_half = std::bind (my_divide,_1,2);  
std::cout << fn_half(10) << '\n';                        // 5
```

- The first parameter of `bind` is the function name. When a normal function is used as an actual parameter, it will be implicitly converted to a function pointer. Therefore ``std::bind` (my_divide,_1,2)` is equivalent to ``std::bind` (&my_divide,_1,2)`;

- _1 means placeholder, located in `<functional>`, `std::placeholders::_1`;

> 2.2 Bind a Class member function

```cpp
struct Foo {
    void print_sum(int n1, int n2)
    {
        std::cout << n1+n2 << '\n';
    }
    int data = 10;
};
int main() 
{
    Foo foo;
    auto f = std::bind(&Foo::print_sum, &foo, 95, std::placeholders::_1);
    f(5); // 100
}
```

- When `bind` binds a class member function, the first parameter represents the pointer of the member function of the object, and the second parameter represents the address of the object.

- Must be explicitly specified `&Foo::print_sum`, because the compiler will not implicitly convert the member function of the object into a function pointer, so you must add `&` before `Foo::print_sum`.

- When using a pointer to an object member function, you must know which object the pointer belongs to, so the second parameter is the address of the object `&foo`;

> 2.3 Bind a reference parameter

By default, those parameters bound, which are not placeholders, are copied to the callable object returned by bind. 
However, similar to lambda, sometimes we want to pass some bound parameters  by reference, or the types of bound parameters cannot be copied.

```cpp
#include <iostream>
#include <functional>
#include <vector>
#include <algorithm>
#include <sstream>
using namespace std::placeholders;
using namespace std;

ostream & print(ostream &os, const string& s, char c)
{
    os << s << c;
    return os;
}

int main()
{
    vector<string> words{"helo", "world", "this", "is", "C++11"};
    ostringstream os;
    char c = ' ';
    for_each(words.begin(), words.end(), 
                   [&os, c](const string & s){os << s << c;} );
    cout << os.str() << endl;

    ostringstream os1;
    // ostream cannot be copied, if you want to pass an object to bind,
    // Instead of copying it, you must use the ref function provided by the standard library
    for_each(words.begin(), words.end(), 
                            bind(print, ref(os1), _1, c));
    cout << os1.str() << endl;
}
```

[Original Address](https://www.jianshu.com/p/f191e88dcc80)


### 15. decltype

The decltype keyword appears to solve the defect that the auto keyword can only infer the type of variables. 
Its usage is very similar to sizeof:

> #### Syntax

```cpp
decltype(expression)
```

In this process, the compiler analyzes the expression and gets its type, but does not actually calculate the value of the expression.

Sometimes, we may need to calculate the type of an expression, for example:

```cpp
auto x = 1;

auto y = 2;

decltype(x+y) z;
```

> #### Usage
 
Tailing return type, auto and decltype cooperate

You might be thinking about whether auto can be used to derive the return type of a function. 
Consider such an example of an addition function. In traditional C++, we must write:

```cpp
template<typename R, typename T, typename U>

R add(T x, U y) {
    
return x+y

}
```

Such code actually becomes very ugly, because the programmer must clearly indicate the return type when using this template function. 
But in fact, we don't know what kind of operation the add() function will do, and what kind of return type it gets.


This problem is solved in C++11. 
Although you may respond right away using decltype to derive the type of x+y, write this code:

```cpp
decltype(x+y) add(T x, U y);
```

But in fact this way of writing does not pass compilation. 
This is because when the compiler reads decltype(x+y), x and y have not yet been defined. 
In order to solve this problem, C++11 also introduces a trailing return type, which uses the auto keyword to post the return type:

```cpp
template<typename T, typename U>

auto add(T x, U y) -> decltype(x+y) {
    
return x+y;

}
```

Starting from C++14, it is possible to directly make ordinary functions have return value derivation, so the following writing becomes legal:

```cpp
template<typename T, typename U>

auto add(T x, U y) {
    
return x+y;

}
```

### 16. Template enhancement

- External template

In traditional C++, templates are only instantiated by the compiler when they are used. 
As long as a fully defined template is encountered in the compiled code in each compilation unit (file), it will be instantiated. 
This produces an increase in compilation time caused by repeated instantiation. 
Also, we have no way to tell the compiler not to trigger template instantiation.


C++11 introduces external templates and expands the original syntax that forces the compiler to instantiate templates at specific locations, so that the compiler can be explicitly told when to instantiate templates:

```cpp
template class std::vector<bool>; // forced instantiation

extern template class std::vector<double>; // Do not instantiate the template in the compiled file
```

- Angle bracket ">"

In traditional C++ compilers, >> is always treated as a right shift operator. 
But in fact, we can easily write the code of the nested template:

```cpp
std::vector<std::vector<int>> wow;
```

This cannot be compiled under the traditional C++ compiler, but starting from C++11, consecutive right angle brackets will become legal and can be compiled smoothly.


- Type alias template

In traditional C++, typedef can define a new name for a type, but there is no way to define a new name for a template. 
Because the template is not a type. 
E.g:

```cpp
template< typename T, typename U, int value>

class SuckType {

public:
    
T a;
    
U b;
    
SuckType():a(value),b(value){}

};

template< typename U>

typedef SuckType<std::vector<int>, U, 1> NewType; // illegal
```

C++11 uses using to introduce the following form of writing, and at the same time supports the same effect as traditional typedef:

```cpp
template <typename T>

using NewType = SuckType<int, T, 1>; // legal
```

- Default template parameters

We may define an addition function:

```cpp
template<typename T, typename U>

auto add(T x, U y) -> decltype(x+y) {
    
return x+y

}
```

But when using it, I found that to use add, you must specify the type of its template parameter every time.

A convenience is provided in C++11 to specify the default parameters of the template:

```cpp
template<typename T = int, typename U = int>

auto add(T x, U y) -> decltype(x+y) {
    
return x+y;

}
```

### 17. Constructor

> #### delegated construction

C++11 introduces the concept of delegated construction, which allows the constructor to call another constructor in the same class, thereby simplifying the code:

```cpp
class Base {
public:
    int value1;
    int value2;
    Base() {
        value1 = 1;
    }
    Base(int value) : Base() {  // 委托 Base() 构造函数
        value2 = 2;
    }

```

> #### Inheritance structure

In the inheritance system, if the derived class wants to use the `constructor` of `base class`, it needs to be explicitly declared in the `constructor`.

If the base class has a large number of different versions of the `constructor`, then a lot of corresponding `"transparent" constructors` have to be written in the `derived class`. 

As follows:

```cpp
struct A
{
  A(int i) {}
  A(double d,int i){}
  A(float f,int i,const char* c){}
  // ...else version of Constructors
}；
struct B:A
{
  B(int i):A(i){}
  B(double d,int i):A(d,i){}
  B(folat f,int i,const char* c):A(f,i,e){}
  // ...else Constructors corresponded with Base-Constructors
}；
```

Comparing to the method above, C++11 `inheritance structure`:

```cpp
struct A
{
  A(int i) {}
  A(double d,int i){}
  A(float f,int i,const char* c){}
  //...
}；
struct B:A
{
  using A::A;
  // a line can inherit from all Base-Constructors
  //......
}；
```

If an `inherited-constructor` is not used by related code, the compiler will not generate real function code for it, which saves more target code space than "transparently" transmitting the various `constructors` of `base class`.

### 18. regular expression 

Its library that provided by C++11, manipulates `std::string` object, initializes the pattern `std::regex` (essentially `std::basic_regex`), and matches through `std::regex_match` to generate `std::smatch` (essentially is a `std::match_results object`).


We briefly introduce the use of this library through a simple example. 
Consider the following regular expression:


[az]+.txt: In this regular expression, [az] means to match a lowercase letter, + can match the previous expression multiple times, so [az]+ can match a string of one or more lowercase letters 
. 
In the regular expression, a. Means to match any character, and. After escape means to match the character., And the final txt means to strictly match the three letters of txt. 
Therefore, the content of this regular expression to be matched is a text file with a file name of pure lowercase letters.

std::regex_match is used to match strings and regular expressions. There are many different overloaded forms. 
The simplest form is to pass in std::string and a std::regex for matching. When the match is successful, it will return true, otherwise it will return false. 

E.g:

```cpp
#include <iostream>
#include <string>
#include <regex>

int main() {
    std::string fnames[] = {"foo.txt", "bar.txt", "test", "a0.txt", "AAA.txt"};
    // In C++, `\` will be used as an escape character in a string. In order to pass `\.` as a regular expression into effect, it is necessary to escape `\` twice, so that there is `\\.`
    std::regex txt_regex("[a-z]+\\.txt");
    for (const auto &fname: fnames)
        std::cout << fname << ": " << std::regex_match(fname, txt_regex) << std::endl;
}
```

Another commonly used form is to pass in the three parameters `std::string` / `std::smatch` / `std::regex` one by one. The essence of `std::smatch` is actually `std::match_results`. 

In the standard library, `std::smatch` is defined as `std::match_results`, which is a match_results of the substring iterator type. 

Use std::smatch to easily obtain the matching results, for example:

```cpp
std::regex base_regex("([a-z]+)\\.txt");
std::smatch base_match;
for(const auto &fname: fnames) {
    if (std::regex_match(fname, base_match, base_regex)) {
        //  The first element of sub_match matches the entire string
        //  The second element of sub_match matches the first bracket expression
        if (base_match.size() == 2) {
            std::string base = base_match[1].str();
            std::cout << "sub-match[0]: " << base_match[0].str() << std::endl;
            std::cout << fname << " sub-match[1]: " << base << std::endl;
        }
    }
}
```


### 19. Rvalue references and move semantics

A simple example:

```cpp

// Use the following copy constructor:
string(const string& that)
{
    size_t size = strlen(that.data) + 1;
    data = new char[size];
    memcpy(data, that.data, size);
}

string a(x);                      // line 1 : A deep copy of x is necessary because we may use x later, x is an lvalues.
string b(x + y);                  // line 2 : The string object produced by the expression is an anonymous object, and there is no way to use it later
string c(some_function_returning_a_string());       // line 3 : func_return cannot be used later

// C++11 introduces a new mechanism called "Rvalue Reference", so that we can directly use rvalue parameters through overloading. 
// All we have to do is write a constructor that takes an rvalue reference as a parameter:

string(string&& that)   // string&& is an rvalue reference to a string
{
data = that.data;
that.data = 0;
}

```

We did not deeply copy the data in the heap memory, but only copied the pointer and nullified the pointer of the source object. 
In fact, we "stole" the memory data belonging to the source object. 

Since the source object is an rvalue and will not be used anymore, the client will not notice that the source object has been changed. 

Here, we do not really copy, so we call this constructor "move constructor" (move constructor), whose job is to transfer resources from one object to another, rather than copy them.


With rvalue references, let's look at the assignment operator:

```cpp
string& operator=(string that)
{
std::swap(data, that.data);
return *this;
}
```

Note that we are passing the value of the parameter that directly, so that will be initialized like any other object. So exactly, how is that initialized? 

For C++ 98, the answer is the copy constructor, but for C++ 11, the compiler will choose between the copy constructor and the transfer constructor based on whether the argument is an lvalue or an rvalue.

If it is a=b, then the copy constructor will be called to initialize that (because b is an lvalue), and the assignment operator will exchange data with the newly created object for a deep copy. 
This is the definition of copy and swap idiom: construct a copy, exchange data with the copy, and let the copy be automatically destroyed within the scope. 
Same here.


If it is a = x + y, then the transfer constructor will be called to initialize that (because x+y is an rvalue), so there is no deep copy here, only efficient data transfer. 
Relative to the parameter, that is still an independent object, but its constructor is useless (trivial), so the data in the heap does not need to be copied, but only transferred. 
There is no need to copy him, because x+y is an rvalue, and again, there is no problem in transferring from the object pointed to by the rvalue.


To summarize: the copy constructor performs a deep copy, because the source object itself must not be changed. 
The transfer constructor can copy the pointer and empty the pointer of the source object. In this form, it is safe because the user can no longer use the object.

We know that transferring lvalues ​​is very dangerous, but transferring rvalues ​​is very safe. 
If C++ can distinguish between lvalue and rvalue parameters from the language level, I can completely eliminate lvalue transfer, or expose the transferred lvalue when calling, so that we don't inadvertently transfer the lvalue.


C++11's answer to this question is an rvalue reference. 
Rvalue reference is a new reference type for rvalue, the syntax is X&&. 
The old reference type X& is now called an lvalue reference.


One of the most useful functions using rvalue reference X&& as a parameter is the transfer constructor X::X(X&& source), its main function is to transfer the local resources of the source object to the current object.


```cpp
//In C++11, std::auto_ptr< T> has been replaced by std::unique_ptr< T >, which is the use of rvalue references. Its transfer constructor :
unique_ptr(unique_ptr&& source)   // note the rvalue reference
{
    ptr = source.ptr;
    source.ptr = nullptr;
}

T// his transfer constructor does the same thing as the copy constructor in auto_ptr, but it can only accept rvalues ​​as parameters.

unique_ptr<Shape> a(new Triangle);
unique_ptr<Shape> b(a);                 // error
// a is an lvalue, but the parameter unique_ptr&& source can only accept rvalues, which is exactly what we need to prevent dangerous implicit transfers
unique_ptr<Shape> c(make_triangle());       // okay
```

Sometimes we want the compiler to treat lvalues ​​as rvalues ​​in order to be able to use the transfer constructor, even if it is a bit unsafe. 
For this purpose, C++11 provides a template function std::move in the header file <utility> of the standard library. 
In fact, std::move simply converts an lvalue to an rvalue, it doesn't transfer anything by itself. 
It just allows the object to be transferred.


Here is how to transfer the left value correctly:

```cpp
unique_ptr<Shape> a(new Triangle);
unique_ptr<Shape> b(a);              // still an error
unique_ptr<Shape> c(std::move(a));   // okay
```

a no longer owns the Triangle object. 
But it doesn’t matter, because by explicitly writing std::move(a), we are very clear about our intention: dear transfer constructor, you can do anything you want to a to initialize c; I will no longer 
A is needed. Regarding a, please feel free to do so.


Of course, if you continue to use a after using mova(a), it will undoubtedly be shooting yourself in the foot or causing serious operating errors.


In short, std::move(some_lvalue) converts an lvalue to an rvalue (can be understood as a type conversion), making the next transfer possible.

A example:

```cpp
class Foo
{
    unique_ptr<Shape> member;

public:

    Foo(unique_ptr<Shape>&& parameter)
    : member(parameter)   // error, because parameter, whose type is an rvalue reference, can only indicate that parameter is a reference to an rvalue, and parameter itself is an lvaluem, Therefore, the above transfer of parameter is not allowed, you need to use std::move to display the conversion to the right value.
    {}
};
```

### 20. nullptr

The purpose of nullptr is to replace NULL.


In a sense, traditional C++ treats NULL and 0 as the same thing, depending on how the compiler defines NULL. Some compilers will define NULL as ((void*)0), and some will directly 
It is defined as 0.


C++ does not allow direct implicit conversion of void * to other types, but if NULL is defined as ((void*)0), then when char *ch = NULL; is compiled, NULL has to be defined as 0.


This will still cause problems, which will cause confusion in the overloaded features of C++. Consider:

```cpp
void foo(char *);

void foo(int);
```

For these two functions, if NULL is defined as 0, then foo(NULL); This statement will call foo(int), which leads to code violation of intuition.


In order to solve this problem, C++11 introduced the nullptr keyword, which is specifically used to distinguish between null pointers and zeros.


The type of nullptr is nullptr_t, which can be implicitly converted to the type of any pointer or member pointer, and can also be compared with them for equality or inequality.


When you need to use NULL, get into the habit of using nullptr directly.

[Original Address](https://blog.csdn.net/jiange_zh/article/details/79356417)

### 21. exception handling and noexcept

Exception handling in C++ is detected at runtime rather than at compile time.

In order to implement runtime detection, the compiler creates additional code, but this prevents program optimization.

In practice, generally two exception throwing methods are commonly used:

- An operation or function may throw an exception;

- It is impossible for an operation or function to throw any exceptions.

In the latter method, throw() is commonly used in previous C++ versions to indicate that it has been replaced by noexcept in C++11.

```cpp
    void swap(Type& x, Type& y) throw()   //C++99
    {
        x.swap(y);
    }
    void swap(Type& x, Type& y) noexcept  //C++11
    {
        x.swap(y);
    }
```

It tells the compiler that no exceptions will occur in the function, which helps the compiler to optimize the program more.

If at runtime, the noexecpt function throws an exception (if the function catches the exception and completes the processing, this is not an exception thrown), the program will terminate directly and call the std::terminate() function, the function 
Internally, std::abort() is called to terminate the program.


The use of noexcept is encouraged in the following situations:

- Move constructor (move constructor)

- Move assignment function

- Destructor.

Here to mention, in the new version of the compiler, the destructor is the keyword noexcept by default. 
The following code can detect whether the compiler adds the keyword noexcept to the destructor.

```cpp
    struct X
    {
        ~X() { };
    };
    
    int main()
    {
        X x;
    
        // This will not fire even in GCC 4.7.2 if the destructor is
        // explicitly marked as noexcept(true)
        static_assert(noexcept(x.~X()), "Ouch!");
    }
```

- Leaf Function. 

Leaf function means that no stack space is allocated within the function, other functions are not called, non-volatile registers are not stored, and exceptions are not handled.

Finally, I would like to emphasize that if you are not in the above situation or you are not sure, do not use noexcept lightly.

[Original Address](https://www.cnblogs.com/sword03/p/10020344.html)


### 22. static_assert

> #### background

In order to reduce the possibility of conflicts with existing program variables, WG21 designed the name of this keyword to be very long, even including underscores. It can be said that the name cannot be ugly, but in some cases, it will still a conflict occurs, such as:

```cpp
static_assert(4<=sizeof(int), "error:small ints");
```

The intent of this line of code is to make sure that the length of the int integer type of the system at compile time (not at runtime) is not less than 4 bytes. If it is less than, the compiler will report an error saying that the integer type of the system is too small. 
This is a valid code in C++11, and it may also be valid in C++98/03, because the programmer may have defined a function called static_assert to determine the int at runtime. 
Is the type size not less than 4. 

Obviously this is completely different from static_assert in C++11.

> #### Introduction

`static_assert` was introduced in C++11 to make assertions during compilation, so it is called static assertions.


> ### syntax 

```cpp
static_assert (constant expression, prompt string).
```

If the value of the first parameter constant expression is `true` (`true` or non-zero), then `static_assert` does nothing, as if it does not exist, otherwise a compilation error will be generated, and the error location is the line of the `static_assert` statement. 
The error prompt is the second parameter prompt string.


> #### instruction

Using `static_assert`, we can find more errors during compilation, use the compiler to enforce certain contracts, and help us improve the readability of compilation information, especially when used in templates.


`static_assert` can be used in the global scope, in the namespace, in the scope of the class, in the scope of the function, almost unlimited use.


When the compiler encounters a `static_assert` statement, it usually calculates its first parameter as a constant expression immediately, but if the constant expression depends on some template parameters, it will delay the calculation until the template is instantiated. 
It is possible to check template parameters.


In terms of performance, since `static_assert` is asserted during compilation and does not generate target code, `static_assert` will not cause any runtime performance loss.

> #### example

For example, static_assert is used in this code to check related configuration parameters during the compilation phase.

```cpp
bool operator==(const Http2PingFields& a, const Http2PingFields& b) {


Static_assert((sizeof a.opaque_bytes) == Http2PingFields::EncodedSize(),


"Why not the same size?");


Return 0 ==


Std::memcmp(a.opaque_bytes, b.opaque_bytes, sizeof a.opaque_bytes);


}
```

> #### Related comparison

We know that in the existing C++ standard, there are two facilities, assert and #error, which are also used to check errors, and there are also some third-party implementations of static assertions.


`assert` is a run-time assertion. It is used to find errors during run-time. It cannot be found in the compile period in advance. It is not mandatory, nor can it improve the readability of compiled information. Since it is a run-time check, it is of course for performance. 
Is influential, so often in the release version, the assert will be turned off;


`#error` can be regarded as a pre-compilation-period assertion, not even an assertion. It can only display an error message during pre-compilation. It can do very little and can participate in the pre-compilation condition check. Because it cannot obtain compilation information, 
Of course, no further analysis can be done.


Then, before `stastic_assert` was submitted to the C++0x standard, in order to make up for the deficiencies of `assert` and `#error`, some third-party solutions appeared, which can be used for static checks during compile time, such as `BOOST_STATIC_ASSERT` and `LOKI_STATIC_CHECK`, but because they are al/l used 
Some tricks implemented by the obscure features of the compiler are not very portable and simple, and they also reduce the compilation speed, and the functions are not perfect. For example, `BOOST_STATIC_ASSERT` cannot define error prompt text, while `LOKI_STATIC_CHECK` requires the prompt text to meet C++ syntax of the type definition.


[Original Address1](https://www.jianshu.com/p/cb9446979645)

[Original Address2](https://developer.aliyun.com/article/173868)
