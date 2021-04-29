# Item 3: Use const whenever possible
`const` specifies a semantic constraint that a particular object should not be modified, and will be enforced by the compiler. It allows you to communicate to both compilers and other programmers that certain values should remain invariant. 

`const` is versatile. It can be used at global or namespace scope, for objects declared static at file function or block scope. Inside classes, it can be used for both static and non-static data types. For pointers, it can specify whether the pointer itself or the data it points to is constant, both, or neither: 

```C++
char greeting[] = "Hello"; 
char *p = greeting;             // non-const pointer,
                                // non-const data
                          
const char *p = greeting;       // non-const pointer,
                                // const data
char* const p = greeting;       // const pointer, 
                                // non-const data
const char* const p = greeting; // const pointer, 
                                // const data

```
```diff
+ Rule of Thumb: If `const` appears to the left of *, what's pointed to is constant; if the word const appears to the right of *, the pointer is constant. 
```
## III. Problem with function-like macros
```C++
//call f with the maximum of a and b
#define CALL_WITH_MAX(a,b) f((a)>(b) ? (a):(b))
```
Some people use macros like this to reduce overhead of function calls, but it has many painful drawbacks： 
1. **Redundancy**: number of parenthesis. Easy to make mistakes.
2. **Undefined behavior**: weird stuff can happen, even though you get all correctly: 


```diff
- Things to Remember
```
* For simple constants, prefer const objects or enums to #defines.
* For function-like macros, prefer inline functions to #defines.
