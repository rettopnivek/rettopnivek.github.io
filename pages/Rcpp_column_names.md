---
layout: page
---

## Setting column names in Rcpp

Rcpp provides an easy way to set the column names of a matrix.  
{% highlight r %}
library(Rcpp)
cppFunction('
  NumericMatrix matEx( ) {
    NumericMatrix mat(2,2);
    mat(0,0) = 1; mat(0,1) = 2;
    mat(1,0) = 3; mat(1,1) = 4;
    
    colnames(mat) = Rcpp::CharacterVector::create("A", "B");
    
    return( mat );
  }
')
{% highlight r %}
