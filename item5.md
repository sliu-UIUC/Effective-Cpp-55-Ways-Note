# Item 5： Know what functions C++ silently writes and calls. 
Compilers will declare their own versions of a copy constructor, a copy assignment operator and a destructor, and even the contructor if you don't declare them 
yourself. All of them will be both puiblic and inline. Thus, writing ```C++ class Empty{}; ``` is equivalent to: 

```C++
class Empty {
public: 
  Empty(){...}                             // default constructor
  Empty(const Empty& rhs) {...}            // copy constructor
  ~Empty(){...}                            // destructor
  
  Empty& operator=(const Empty& rhs) {...} //copy assignment operator
};
```
These functions are generated only when needed, but its pretty easy to cause each function to be generated: 
```C++
Empty e1;        // default constructor;
                 // destructor

Empty e2(e1);    // copy constructor 
e2 = e1;         // copy assignment operator
```
* Default constructor and destructor: give compilers a place to put "behind the scenes" code such as invocation of constructors/destructors of base classes and non-static data members. The generated destructor is non-virtual unless it's for a class inheriting from a base class than itself declares a virtual destructor (in which case the function's virtualness comes from the base class).
* copy constructor & copy assignment operator: compiler-generated version of them simply copy each non-static data member of the source object over to the target object. Copy constructor is called when a new object is created from an existing object, as a copy of the existing object. Assignment operator is called when an already initialized object is assigned a new value from another existing object.

** Note: when member variables have reference or const type, default generated copy assignment opeartor will not work. C++ does not provide a way to make a reference refer to a different object, and ``const`` member cannot be changed. Compilers also reject implicity copy assignment operators in derived classes that inherit from base classes declaring the copy assignment operator private (can't invoke member functions). 

```diff
- Things to Remember
```
* Compilers may implicitly generate a class's default constructor, copy constructor, copy assignment opeartor, and destructor.
