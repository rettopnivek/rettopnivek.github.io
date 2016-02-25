---
layout: page
---

## Printing to the console in Rcpp

Here's a simple example of how to print to the console window with a simple Rcpp function. This also provides an example of the versatile 'cppFunction'.  
```
library(Rcpp)
cppFunction('
  void helloWorld( ) {
    Rcpp::Rcout << "Hello world!" << std::endl;
  }
')
```
