# Ceres Solver with VarPro
This repository contains a patch for the [Ceres Solver](http://ceres-solver.org/) that implements the variable projection (VarPro) method using Ruhe and Wedin algorithm 2. The code is provided without any warranty. If using this patch, please cite our work as shown below.
```
@inproceedings{hong17, 
    author = {J. H. Hong and C. Zach and A. Fitzgibbon}, 
    booktitle = {2017 IEEE Conference on Computer Vision and Pattern Recognition (CVPR)}, 
    title = {Revisiting the Variable Projection Method for Separable Nonlinear Least Squares Problems}, 
    year = {2017}, 
    pages = {5939--5947}, 
    doi = {10.1109/CVPR.2017.629}, 
    ISSN = {1063-6919}, 
    month = {July}
}
```

## Additional option parameters
Below lists a series of additional option parameters added to this patch.
```
InnerIterationType inner_iteration_type: EMBEDDED_POINT_ITERATION (default), RUHE_WEDIN_ALGORITHM_2
```
```
RadiusUpdateType trust_region_radius_update_type: TRUST_REGION_UPDATE (default), TRADITIONAL_UPDATE
```
```
bool use_linear_inner_iterations: true, false (default)
```
```
bool use_block_qr_for_rw2: true, false (default)
```
```
DampingType lm_damping_type: MARQUARDT (default), LEVENBERG
```
```
bool initialize_with_inner_iteration: true (default), false
```

For the moment, a subset of these options (e.g. RUHE_WEDIN_ALGORITHM_2 + TRADITIONAL_UPDATE) is required to configure Ruhe and Wedin 2 as the expected cost change used in TRUST_REGION_UPDATE is not properly calcuated for separable nonlinear least squares (SNLS) problems.

## How to run VarPro (RW2)?
1. Make sure that your nonlinear least squares problem is a type of separable nonlinear least squares problem of the general form
```
Vector residuals(Vector u, Vector v) { return F(u) + G(u) * v; } 
```
where the `*` operator is matrix/vector multiplication.  Note that this includes matrix/tensor `v` too, via reshaping.  You don't need to explicitly reshape, it just needs to be linear in `v`.
For more details, see our [paper](https://pdfs.semanticscholar.org/43b4/f13b2beb3d224d8e8a1b67f0192ccf014ee8.pdf).

2. Check that your inner iteration ordering (whether automatically or manually set) eliminates the linear set of parameters (`v` above).

3. Set the following option parameters as follows:

```
inner_iteration_type:           RUHE_WEDIN_ALGORITHM_2  (compulsory)
trust_region_radius_update      TRADITIONAL_UPDATE      (compulsory for now)
use_linear_inner_iterations:    true                    (optional but preferred)
use_block_qr_for_rw2:           true                    (optional but preferred, improves conditioning of the reduced GN matrix)
lm_damping_type:                LEVENBERG               (optional, sometimes MARQUARDT can also be fine.)
initialize_with_inner_iteration true                    (optional, just to make it more ``VarPro'')

```

## Bug report
Please email jhh37@cantab.net for issues related to this patch.
