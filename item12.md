# Item 12: Copy all parts of an object

Compiler auto generates copy constructor and copy assignment operators that do what they are supposed to do. They copy all the data of the object being copied. However, if copy constructor is declared by user, something can go wrong and compiler won't tell why. Here is an example: 

```C++
class Customer {                    // make a log entry
public: 
  ...
  Customer(const Customer& rhs); 
  Customer& operator=(const Customer& rhs);
  ...
private:
  std::string name; 
};

Customer::Customer(const Customer& rhs) : name(rhs.name)    // copy rhs's data
{
  logCall("Customer copy constructor");    
}

Customer& Customer::operator=(const Customer& rhs)
{
  logCall("Customer copy assignment operator"); 
  name = rhs.name;            // copy rhs's data
  return *this;
}
```

The above code is fine, until another data member is added to `Customer`: 
```C++
 class Date {...}; 
 
 class Customer {
 public: 
  ...
 private: 
  std::string name; 
  Date lastTransaction;
 }
```
Now the self-delcared copy constructor is doing a partial copy, that is without copying `lastTransaction`. If one has a self delcared copy functions and add a data member to a class, one has to make update to the copy functions in order to copy all data properly. Otherwise, most compiler won't tell you and not even at maxmimal warning level. 

One of the most insidious ways this issue can rise is through inheritance: 

```C++
class PriorityCustomer: public Customer {   // a deried class
public: 
  ...
  PriorityCustomer(const PriorityCustomer& rhs); 
  PriorityCustomer& operator=(const PriorityCustomer& rhs);
  ...
private: 
  int priority;
};

PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs): priority:(rhs.priority) {
  logCall("PriorityCustomer copy constructor");
}

PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs) {
  logCall("PriorityCustomer copy assignment operator"); 
  priority = rhs.priority; 
  return *this;
}
```

The above copy funcitions only copies the data member that `PriorityCustomer` declares, but every `PriorityCustomer` also contains a copy of the data members it inherits from `Customer`,and those data are not being copied. The copy constructor specifies no arguments to be passed to its base class constructor (making no mention of `Customer` on its member initialization list), so the `Customer` part of the `PriorityCustomer` object will be initialized by the `Customer` constructor taking no argument, by default constructor (if default constructor is missing, code won't compile). 

Thus, one must take care of copying the base class parts. Since those parts are typically private, you won't access them directly. Instead, derived class copying functions must invoke their corresponding base class functions: 

```C++
PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs) 
: Customer(rhs),              // invoke base class copy constructor
priority(rhs.priority)  
{
  logCall("PriorityCustomer copy constructor");
}

PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs) 
{
  logCall("PriorityCustomer copy assignment operator"); 
  Customer::operator=(rhs);    // assign base class parts
  priority = rhs.priority; 
  return *this;
}
```
In practice, the two copy functions will have similar body, and trying to avoid code duplication is reasonable. However, you should not have one copy function call the other. It is nonsensical to have a copy assignment operator call copy constructor, you'd be trying to construct an object that already exists. Similarly, it makes no sense either to have copy constructor calls a copy assignment operator: a copy constructor initializes objects, but a copy assignment operator applies asssignments on an object that have already been initialized.

To avoid code redundancy, just have a third function (typically private and called `init`) that both calls. This way is safe.


```diff
- Things to remember
```

* Copying functions should be sure to copy all of an object's data members and all its base class parts
* Don't try to implement one of the copying functions in terms of the other. Instead, put common funcitionality in a third function that both call. 
