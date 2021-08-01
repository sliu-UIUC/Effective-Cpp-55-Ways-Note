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
