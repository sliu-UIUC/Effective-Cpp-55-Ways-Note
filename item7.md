# Item 7： Declare destructors virtual in polymorphic base classes. 
Sometimes we just want to access certain object without worrying about the details of it, so a `factory function`, which is a function that returns a base
class pointer to a newly-created derived class object - can be used to return a pointer to certain object. For instance: 

```C++
class TimeKeeper {
public: 
  TimeKeeper();
  ~TimeKeeper();

};

TimeKeeper* getTimeKeeper();      // returns a pointer to a dynamically allocated object of a class derived from TimeKeeper
```
objects returned by `getTimeKeeper` are stored in heap, so its important that they need to be properly deleted after use to avoid memory leak: 

```C++
TimeKeeper *ptk = getTimeKeeper();     // get dynamically allocated object 
                                       // from TimeKeeper hierarchy
...                                    // use it 
delete ptk;                            // release it to avoid resource leak
```

This, however, causes undefined behavior. The reason is that `getTimeKeeper` returns a pointer to a derived class object (a timekeepr instance), that object is being deleted via a base class pointer (a TimeKeeper* pointer), and the base class (TimeKeeper) has a `non-virtual destructor`. This is disastrous in C++ because what usually happen at runtime is that the derived part of the object is never destroyed (partially destroyed). 

The solution is to give the base class a `virtual destructor`. `delete` then will now destroy the entire object, including all its derived class parts: 

```C++
class TimeKeeper {
public: 
  TimeKeeper();
  virtual ~TimeKeeper();

};

TimeKeeper *ptk = getTimeKeeper();                          
...                                   
delete ptk;                            // now behaves correctly
```
classes like this generally contain virtual functions because they allow customization of derived class implementation. If a class has virtual functions, it should usually have a virtual destructor. 

On the other hand, if a class does not contain virtual functions, it usually indicates it is not meant to be used as a base class. Making a virtual destructor in such a class can be problematic: 

```C++
class Point {
public: 
  Point(int x, int y); 
  ~Point();
  
private: 
  int x, y;
};
```
Such a point object can typically fit into a 64-bit register with each int occupying 32-bit. It can also be passed as a 64-bit quantity to functions written in other languages like C. If point;s destructor is made in virtual, the size a `Point` object occupies will increase, because the impplementation of virtual functions requires that objects carry information that can be used at runtime to determine which virtual functions should be invoked. It typically has a map that maps the virtual table pointer `vptr` to virtual table `vtbl`. Having its destructor declared virtual not only makes it no longer fit in one register, it also makes the port to other languages impossible because languages like C do not have `vptr`. 

```diff
+ Declare a virtual destructor in a class iff that class contains at least one virtual function.
```

It is also possible to have non-virtual destructor problem with the absense of virtual functions. For instance: 

```C++
class SpecialString: public std::string {         // bad: std::string has a non-virtual destructor

}; 
```
the code makes little sense since `string` type contains no virtual function and should not be used as base class. If at any point in the application, a pointer to `SpecialString` is converted into a pointer to `string`, and `delete` is used on the string pointer, it causes undefined behavior. 

```C++
SpecialString *pss = new SpecialString("Impending Doom");
std::string *ps; 
...
ps = pss;        //SpecialString* => std::string*
...
delete ps;       // undefined! in pratice *ps's SpecialString resources will be leaked, because its destructor is not called
```
C++ does not have a mechanism preventing inheritance from classes with non-virtual destructor, unlike Java's `final` or C#'s `sealed` classes. 


```diff
+ Do not inherit from classes with no virtual function. 
```

A pure virtual function makes a class abstract. Sometimes it is useful to use a pure virtual destructor to make a class abstract rather than having any additional 
pure virtual classes: 
```C++
class AWOV {            // AWOV = "Abstract w/o Virtuals" 
public:          
  virtual ~AWOV() = 0;  // declare a oyre virtual destructor 
}; 
```
This way the class is abstract without destructor problem. However, a `definition` must be provided for the pure virtual destructor: 
```C++
AWOV::~AWOV(){}         
```
this is because the way destructor work is that most derived class's destructor is called first, then the destructor of each base class is called. Compilers will generate a call to `~AWOV` from its derived classes' destructors, so you have to be sure to provide a body for the function, otherwise linker will complain. 

```diff
- Things to Remember
```
* Polymorphic base classes should declare virtual destructors. If a class has any virtual functions, it should have a virtual destructor. 
* Classes not designed to be base classes or not designed to be used polymorphically should not declare virtual destructors.
