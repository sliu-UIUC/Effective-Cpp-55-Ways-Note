# Item 11 : Handling assignment to self in operator=
This example of assignment to self is obvious, seemingly silly but legal.
```C++
class Widget {...}
Widget w; 
...
w = w;            // assignment to self
```
Some assignment to self isn't so recognizable: 

```C++
/*
  aliasing
*/
a[i] = a[j];     // potential assignment to self. (i = j)

*px = *py;       // potential assignment to self. (px and py point to the same thing)
```
The two objects don't even need to be declared to be of the same type if they are from the same hierarchy, because a base class reference or pointer can refer or point to an object of a derived class type: 

```C++
class Base {...}
class Derived: public Base {...}

void doSomething(const Base& rb,           //rb and *pd might actually be the same object
                 Derived* pd); 

```
Assignment operations being self-assignment safe is important to make sure resource-managing objects behave well when copied. When do resource management, resouce can be released before they were done being used. Suppose the following example: 
```C++
class Bitmap {...}
class Widget {
  ...
private: 
  Bitmap *pb;   // ptr to a heap-allocated object
};

Widget& Widget::operator=(const Widget& rhs)  // unsafe implementation of operator=
{
  delete pb;                                  // stop using current bitmap
  pb = new Bitmap(*rhs.pb);                   // start using a copy of rhs's bitmap
  return *this;                               // see item 10
}
```
The problem here is `rhs` and `*this` can be the same object. When they arem `delete` not only destroyes the bit map for the current object, it destroys the bitmap for `rhs` too. In the end `Widget` will hold a pointer to a deleted object. 

A traditional way to prevent this error is to check for assignment to self via an `identity test` at the top of operator=:
```C++
Widget& Widget::operator=(const Widget& rhs)  
{
  it(this == &rhs) return *this;           // identity test: if self-assignment, do nothing
  
  delete pb;                                 
  pb = new Bitmap(*rhs.pb);                   
  return *this;                          
}
```
The above code works, but not exception safe: if `new Bitmap` yields an exception (either having insufficient memory for allocation or exception from its copy constructor), the `Widget` will wind up holding a pointer to a deleted `BitMap`, which cannot be safely read or delete. 

However, making `operator=` exception-safe typically makes it assignment safe. As a result, it's increasingly common to deal with issues of self-assignment by ignoring them, focusing instead on achieving exception safety. In this case (and many cases), we reorder the statements to carefully not delete `pb` until after we have copied what it points to: 
```C++
Widget& Widget::operator=(const Widget& rhs)  
{
  Bitmap *pOrig = pb;            // remember original pb 
  pb = new Bitmap(*rhs.pb);      // make pb point to a copy of *pb   
  delete pOrig;                  // delete the original pb
  
  return *this;                          
}
```
Now if `new Bitmap` throws an exception, pb (and the `Widget` it is inside of) remains unchanged. Even without identity test, this code is safe because we make a copy of original object. 

An alternative to manually ordering statements is to use "copy and swap" tecnique: 

```C++
class Widget {
  ...
  void swap(Widget& rhs);         // exchange *this's and rhs's data (detailed in item 29) 
};

Widget& Widget::operator=(const Widget& rhs)  // unsafe implementation of operator=
{
  Widget temp(rhs)                           // make a copy of rhs's data 
  swap(temp);                                // swap *this's data with the copy's
  return *this;                              
}
```
A variation on this theme takes advantage of the facts that `(a) a class's copy assignment operator may be declared to take its argument by value` and `(b) passing something by value makes a copy of it`: 
```C++
Widget& Widget::operator=(Widget rhs)  // rhs is a copy of the object passed in (pass by val)
{
  swap(rhs);                         
  return *this;                              
}
```
This sacrifices some clarity, but by moving the copying operation from the body of the function to construction of the parameter, it's a fact that compilers can sometimes generate more efficient code. 

```diff
- Things to Remember
```
* Make sure `operator=` is well-behaved when an object is assigned to itself. Techniques include comparing addresses of source and target objects, careful statement ordering, and copy-and-swap. 
* Make sure that any function operating on more than one object behaves correctly if two or more of the objects are the same. 

