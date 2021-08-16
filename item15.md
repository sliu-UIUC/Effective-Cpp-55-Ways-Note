# Item 15: Provide access to raw resources in resource-managing classes

C++ have default resource-managing classes that prevent resource leaks, but from time to time one have to use some APIs that will diretly access raw resources and will then have to deal with raw resources. 

