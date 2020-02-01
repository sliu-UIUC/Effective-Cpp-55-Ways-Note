# Item 1: View C++ as a federation of languages

Initially C++ was just C with object-oriented features, or "C with Classes". Today's C++ is a *multiparadigm programming language*, one supporting a **combination of procedural, object-oriented, functional, generic and metaprogramming features**. This power and flexibility,at the same time of making C++ unique, has also caused **confusions**. Instead, treating C++ not as a single language but as a federation of related languages makes things easier, since each sublanguage's rule is simple, straightforward to understand. Each one of the 4 sub-languages have different rules specifically as follows:

* **C.** Deep down C++ is still based on C. **Blocks, statements, the preprocessor, build-in data types, arrays, pointers** all come from C. The "C part" of C++ reflects more limited scope: **no template, no exceptions, no overloading, etc**.

* **Object-Oriented C++.** What C with Classes was all about: **classes (including constructors&destructors), encapsulation, inheritance, polymorphism, virtual function (dynamic binding)**, etc. The part of C++ to which the classic rules for object-oriented design most directly apply. 

* **Template C++**. Generic programming part of C++. Templates are powerful and give rises to a completely new paradigm ***template metaprogramming***(TMP). Most programmers have the least experience with, and rules of TMP ralely interact with mainstream C++ programming.

* **The STL.** Standard template library. Its conventions meshed beautifully regrading **containers, iterators, algorithms, and function objects**. When you're working with the STL, its conventions has to be followed.  
