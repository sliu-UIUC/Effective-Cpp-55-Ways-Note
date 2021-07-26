# Item 10: Have assignment operators return a reference to *this. 

C++ allows you to chain assignments together: 

```C++
int x, y, z; 
x = y = z = 15; 
```
The assignment is right-associative, so the above assignment chain is parsed like this: 

```C++
x = (y = (z = 15)); 
```
where 15 is first assigned to `z` and then the result of the assignment is assigned to `y` and then that assigned to `x`. 
The way this is implmented is that the assignment **returns a reference** to the left-hand argument, and that is the convention that should be 
followed when one implement assignment operators for classes: 

```C++
class Widget {
public: 
  ...
  Widget& operator=(const Widget& rhs) {  // return type is a reference to the current class
    ...
    return *this;                         // return the left-hand object
  }
  ...
};

```
The convention also aplies to `+=`, `-=`, `*=`, etc (for instance: `Widget& operator=(const Widget& rhs)`) and when operator's parameter type 
is unconventional (`Widget& operator=(int rhs)`).
This is only a convention as the code doesn't follow can still work and compile. But it should be used whenever possible as it is the convention
followed by all the built-in types as well as standard template library (STL). 

```diff
- Things to Remember
```

* Having assignment operators return a reference to *this.
