# Const uses in c++ language 
When modifying a data declaration, the const keyword specifies that the object or variable **is not modifiable**.


1-The const keyword specifies that a variable's value is constant and tells the compiler to prevent the programmer from modifying it.
```
// constant_values1.cpp
int main() {
   const int i = 5;
   i = 10;   // C3892
   i++;   // C2105
}
```

2-In C++, you can use the const keyword instead of the #define preprocessor directive to define constant values. Values defined with const are subject to type checking, and can be used in place of constant expressions. In C++, you can specify the size of an array with a const variable as follows:
```
// constant_values2.cpp
// compile with: /c
const int maxarray = 255;
char store_char[maxarray];  // allowed in C++; not allowed in C
```
3-In C++, you can use the const keyword instead of the #define preprocessor directive to define constant values. Values defined with const are subject to type checking, and can be used in place of constant expressions. In C++, you can specify the size of an array with a const variable as follows:

```
// constant_values2.cpp
// compile with: /c
const int maxarray = 255;
char store_char[maxarray];  // allowed in C++; not allowed in C
```

4-In C, constant values default to external linkage, so they can appear only in source files. In C++, constant values default to internal linkage, which allows them to appear in header files.

The const keyword can also be used in pointer declarations.
```
// constant_values3.cpp
int main() {
   char *mybuf = 0, *yourbuf;
   char *const aptr = mybuf;
   *aptr = 'a';   // OK
   aptr = yourbuf;   // C3892
}
```
5-A pointer to a variable declared as const can be assigned only to a pointer that is also declared as const.
```
// constant_values4.cpp
#include <stdio.h>
int main() {
   const char *mybuf = "test";
   char *yourbuf = "test2";
   printf_s("%s\n", mybuf);

   const char *bptr = mybuf;   // Pointer to constant data
   printf_s("%s\n", bptr);

   // *bptr = 'a';   // Error
}
```
6-You can use pointers to constant data as function parameters to prevent the function from modifying a parameter passed through a pointer.
For objects that are declared as const, you can only call constant member functions. This ensures that the constant object is never modified.
```
birthday.getMonth();    // Okay
birthday.setMonth( 4 ); // Error
```
#const member functions
Declaring a member function with the const keyword specifies that the function is a "read-only" function that does not modify the object for which it is called. A constant member function cannot modify any non-static data members or call any member functions that aren't constant.To declare a constant member function, place the const keyword after the closing parenthesis of the argument list. The const keyword is required in both the declaration and the definition.
```
// constant_member_function.cpp
class Date
{
public:
   Date( int mn, int dy, int yr );
   int getMonth() const;     // A read-only function
   void setMonth( int mn );   // A write function; can't be const
private:
   int month;
};

int Date::getMonth() const
{
   return month;        // Doesn't modify anything
}
void Date::setMonth( int mn )
{
   month = mn;          // Modifies data member
}
int main()
{
   Date MyDate( 7, 4, 1998 );
   const Date BirthDate( 1, 18, 1953 );
   MyDate.setMonth( 4 );    // Okay
   BirthDate.getMonth();    // Okay
   BirthDate.setMonth( 4 ); // C2662 Error
}
```
#How to use ampersands in C++

Do you want to get a C++ question every day to get prepared for your next interview? Sign up for DailyCppInterview for free!

In one of my previous articles, I wrote about Scott Meyer's Effective Modern C++ and that with its focus on C++11/14 it's like discovering a completely new language. I already wrote about trailing return type declaration. Now it's time to review what usages you might have in C++ for ampersands (&).

Let's start with the good old, better-known usages:

***1-& to declare a reference to a type***
**2-& to get the address of a variable**
***3-& as a bit-wise operator***
***4-&& in a conditional expression***
These are not new, but "repetition is the mother of learning".

Use & to declare a reference to a type
If you use & in the left-hand side of a variable declaration, it means that you expect to have a reference to the declared type. It can be used in any type of declarations (local variables, class members, method parameters).
```
std::string mrSamberg("Andy");
std::string& theBoss = mrSamberg;
```
This doesn't just mean that both mrSamberg and theBoss will have the same value, but they will actually point to the same place in the memory.
``` 
std::string mrSamberg("Andy");
std::string* theBoss;

theBoss = &mrSamberg;
```

The end result of the previous snippet is the same as previously. Although the type of theBoss is different. Previously it was a reference, now it's a pointer. The main difference is that a pointer can be null, while a reference must point to a valid value.

&& in a (logical) expression is just the C-style way to say and. That's it.

Use && for declaring rvalue references
Declaring a what? - you might ask. Okay, so let's clarify first what are lvalues and rvalues and what are the differences.

According to Eli Bendersky:

An lvalue (locator value) represents an object that occupies some identifiable location in memory (i.e. has an address).

rvalues are defined by exclusion, by saying that every expression is either an lvalue or an rvalue. Therefore, from the above definition of lvalue, an rvalue is an expression that does not represent an object occupying some identifiable location in memory.

Let's take one example to show both an lvalue and an rvalue.
auto mrSamberg = std::string{"Andy"};
mrSamberg represents an lvalue. It points to a specific place in the memory which identifies an object. On the other hand, what you can find on the right side std::string{"Andy"} is actually an rvalue. It's an expression that can't have a value assigned to, that's already the value itself. It can be only on the right-hand side of an assignment operator.

For a better and deeper explanation, please read Eli's article.

Although rvalues can only appear on the right-hand side, still one can capture references to them. Those "captures" are called rvalue references and such variables have to be declared with double ampersands (&&). Binding such temporaries are needed to implement move semantics and perfect forwarding. (I'll explain perfect forwarding and move semantics in a later article.)

Use && for declaring universal references
The bad news is that && after a type might or might not mean that you are declaring an rvalue reference. In certain circumstances, it only means something that [Scott Meyers] calls a universal reference in his Effective Modern C++.

What are those circumstances? Briefly, if type deduction takes place, you declare a universal reference, if not an rvalue reference.
```
Vehicle car;
auto&& car2 = car; // type deduction! this is a universal reference!
Vehicle&& car3 = car; // no type deduction, so it's an rvalue reference
There is another possibility, it's in case of templates. Taking the example from Effective Modern C++:
template<typename T>
void f(std::vector<T>&& param);     // rvalue reference

template<typename T>
void f(T&& param); // type deduction occurs, so this is a universal reference!
```
#Use & or && for function overloading

Since C++11 you can use both the single and double ampersands as part of the function signature, but not part of the parameter list examples:
```
void doSomething() &;
void doSomething() &&;
auto doSomethingElse() & -> int;
auto doSomethingElse() && -> int;
```
What this means is that you can limit the use of a member function based on whether *this is a lvalue or an rvalue. So you can only use this feature within classes, of course. Let's expand our example.
```
class Tool {
public:
  //
  void doSomething() &; // used when *this is a lvalue
  void doSomething() &&; // used when *this is a rvalue
};

Tool makeTool(); //a factory function returning an rvalue

Tool t; // t is an lvalue

t.doSomething(); // Tool::doSomething & is called

makeTool().doSomething(); // Tool::doSomething
```
 && is called
When would you use this kind of differentiation? Mostly when you'd like to optimize your memory footprint by taking advantage of move semantics. In a later post, I'll go more deeply on that.
# References:
https://dev.to/sandordargo/how-to-use-ampersands-in-c-3kga
https://www.certiology.com/tutorials/c-plus-plus-tutorial/const-keyword-in-c-plus-plus-programming.html
