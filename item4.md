# Item 4: Make sure that objects are initialized before they're used

Always initialize variables when declaring! For example, Uninitialized `int` variables are sometimes guaranteed to be initialized to zero, but sometimes not. Reading unitialized values thus can yield undefined behavior, and in some platforms the act of reading uninitialized values can halt your program. 

```C++
int x = 0;                             // manual initialization of an int
const char *text = "A C-style string"; // manual initialization of a pointer

double d;                              // initialization by reading from an input stream 
std::cin >> d;

```

```diff
- Things to Remember
```
* Manually initialize objects of build-in types, because C++ does not guarantee to initialize everytime. 
* In a constructor, prefer use of the member initialization list to assignment inside the body of the constructor. List data members in the initialization list in the same order they're declared in the class. 
* Avoid initialization order problems across translation units by replacing non-local static objects with local static objects.
