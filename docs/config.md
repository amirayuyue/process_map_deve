# Force Field
?> GAFF
## Fitting algorithm
- [Dual annealing](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.dual_annealing.html) from scipy 
  - Find the global minimum of a function using Dual Annealing.
  - scipy.optimize.dual_annealing(func, bounds, args=(), maxiter=1000, minimizer_kwargs=None, initial_temp=5230.0, restart_temp_ratio=2e-05, visit=2.62, accept=- 5.0, maxfun=10000000.0, seed=None, no_local_search=False, callback=None, x0=None, local_search_options=None)