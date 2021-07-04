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

> [Original Address](https://en.cppreference.com/w/cpp/language/range-for)

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

Example:
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

> [Original Address](https://www.geeksforgeeks.org/lambda-expression-in-c/)

### 6.Lambda expression

C++ 11 introduced lambda expression to allow us write an inline function which can be used for short snippets of code that are not going to be reuse and not worth naming. 

> ##### Syntax:

    In its simplest form lambda expression can be defined as follows:
    [ capture clause ] (parameters) -> return-type  
    {   
    definition of method   
    } 

Generally return-type in lambda expression are evaluated by compiler itself and we don’t need to specify that explicitly and -> return-type part can be ignored but in some complex case as in conditional statement, compiler can’t make out the return type and we need to specify that.

> ##### Usage

Various uses of lambda expression with standard function are given below :// C++ program to demonstrate lambda expression in C++
```cpp
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

> [Original address](/https://www.geeksforgeeks.org/lambda-expression-in-c/)


### 7.Smart pointer -- shared_ptr

> #### Intro

- With reference counting mechanism, support multiple pointer objects to point to the same piece of memory (shared)

- Provide the swap() member function to exchange two objects of the same type, such as: 

    ```cpp

    shared_ptr<int> p1(new int(1));

    shared_ptr<int> p2(new int(2));

    p1.swap(p2); //After exchange p1=2,p2=1

    cout<< *p1 <<endl; //print 2

    cout<< *p2 <<endl; //print 1

    ```

-  Provide the unique() member function to determine whether the address of the pointer is referenced by other pointers

- Provide the get() member function to get the address pointed to by the pointer object

- Provide the reset() member function, set the address of its own pointer object to NULL, and count the reference to -1 (when the count is 0, it will automatically delete the memory)

- Provide the use_count() member function, which can be used to view the number of reference counts, such as:

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

> [Original Address](https://cloud.tencent.com/developer/article/1099957)
