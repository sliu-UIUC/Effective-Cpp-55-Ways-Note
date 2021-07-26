# Item 11 : Handling assignment to self in operator=

```C++
class Widget {...}
Widget w; 
...
w = w;            // assignment to self
```
This example of assignment to self is obvious, seemingly silly but legal. Some assignment to self isn't so recognizable: 

```C++
a[i] = a[j];     // potential assignment to self. (i = j)

*px = *py;       // potential assignment to self. (px and py point to the same thing)
```


```diff
- Things to Remember
```


