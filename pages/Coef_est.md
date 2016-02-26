---
layout: page
---

## Matrix multiplication for weighting coefficients
Often we want to efficiently determine the sum of a set of independent variables weighted by a set of coefficients across a large number of trials (e.g. linear regression). Here is a RcppArmadillo function to carry out such an operation.
<pre><code class="R">
library(Rcpp)
cppFunction('
  NumericMatrix coef_est( arma::mat X, 
                          NumericVector param, 
                          IntegerMatrix nCov, 
                          IntegerVector parSel) {
    // Purpose:
    // Allows the efficient computation of the set 
    // of parameter values per trial via matrix algebra 
    // Arguments:
    // X      - a N by K design matrix
    // param  - a vector of M coefficients
    // nCov   - a 2 by P matrix giving the indices
    // parSel - A vector of K indices indicating which 
    //          of the M coefficients correspond to the 
    //          kth column of X (K >= M)
    // Returns:
    // A N x P matrix of parameters
    
    int N = X.n_rows;
    int K = X.n_cols;
    int P = nCov.ncol();
    
    // Define an output matrix
    arma::mat out(N,P);
    
    // Define a arma matrix for the parameters
    arma::mat pm(K,P);
    pm.fill(0.0);
    
    for (int p = 0; p < P; p++) {
      for (int ps = nCov(0,p)-1; ps < nCov(1,p); ps++) {
        pm(ps,p) = param( parSel(ps)-1 );
      }
    }
    
    out = X*pm;
    
    return( wrap(out) );
  }
  ', depends = "RcppArmadillo"
)
</code></pre>  
This is a very useful method to handle a large number of conditions when creating your own likelihood function. However, it isn't intuitive on how to use this function, so here's an example.
### Example
Suppose we have 4 conditions with 2 trials each, giving a total of 8 trials. We're trying to determine the mean $\mu$ and variance $\sigma$ for each trial. Let 
$$
\mu_i = \beta_0 + X_i^2 \beta_1 + X_i^3 \beta_2 + X_i^4 \beta_2
$$
for trial $$i$$, where $$X^j$$ is a dummy coded variable for condition $$j$$. We can implement this with the following code:
<pre><code class="R">
# We'll use an intercept and 3 dummy coded variables to represent how mu changes across
# trials. For sigma, we'll use an intercept, since it does not change across trials.
# Hence, we have a 8 x 5 design matrix.
X = diag(4)
X = rbind( X, X )
X[,1] = 1
X = cbind( X, 1 )
# The first 4 columns of X are used to determine mu, and the final column is for sigma.
# We indicate this with the nCov parameter
nCov = cbind( c(1,4), c(5,5) )
# We have a total of 4 coefficients ( 3 for mu, 1 for sigma). Here are some example values.
param = c( 1, -.5, 1, 1 )
# The final coefficient for mu is applied both for condition 3 and 4.
# We can indicate this using the parSel parameter.
parSel = c( 1, 2, 3, 3, 4 )
# We then calculate the values of mu and sigma for the 8 trials.
# These values can then be plugged into a likelihood function, for example.
coef = coef_est( X, param, nCov, parSel )
</code></pre>  
