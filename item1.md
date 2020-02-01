# Item 1: View C++ as a federation of languages

Initially C++ was just C with object-oriented features, or "C with Classes". Today's C++ is a *multiparadigm programming language*, one supporting a **combination of procedural, object-oriented, functional, generic and metaprogramming features**. This power and flexibility,at the same time of making C++ unique, has also caused **confusions**. Instead, treating C++ not as a single language but as a federation of related languages makes things easier, since each sublanguage's rule is simple, straightforward to understand. Each one of the 4 sub-languages have different rules specifically as follows:

* **C.** Deep down C++ is still based on C. **Blocks, statements, the preprocessor, build-in data types, arrays, pointers** all come from C. The "C part" of C++ reflects more limited scope: **no template, no exceptions, no overloading, etc**.

* **Object-Oriented C++.** What C with Classes was all about: **classes** (including **constructors & destructors**), **encapsulation, inheritance, polymorphism, virtual function (dynamic binding)**, etc. The part of C++ to which the classic rules for object-oriented design most directly apply. 

* **Template C++**. Generic programming part of C++. Templates are powerful and give rises to a completely new paradigm ***template metaprogramming***(TMP). Most programmers have the least experience with, and rules of TMP ralely interact with mainstream C++ programming.

* **The STL.** Standard template library. Its conventions meshed beautifully regrading **containers, iterators, algorithms, and function objects**. When you're working with the STL, its conventions has to be followed.  

### Importance of 4 sublanguages: 
* Cases where effective programming requires you change strategy when switching from one sublan to another
  * In **build-in** (i.e., **C-like**) types, **Pass-by-value** is **generally more effecient** than pass-by-reference for build-in (i.e., C-like) types. 
  * In **STL**,  **Pass-by-value** is more efficient for iterators and function objects, since they are modeled on pointers in C.
  * In **Object-oriented C++**, user-defined constructors and destructors means **pass-by-reference-to-cost** is usually better. 
  * In **Template C++**, **pass-by-reference-to-cost** should be used because you dont know the type of object you are dealing with.
