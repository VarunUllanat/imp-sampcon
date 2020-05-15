[![Build Status](https://travis-ci.org/salilab/imp-sampcon.svg?branch=master)](https://travis-ci.org/salilab/imp-sampcon)
[![codecov](https://codecov.io/gh/salilab/imp-sampcon/branch/master/graph/badge.svg)](https://codecov.io/gh/salilab/imp-sampcon)

# imp-sampcon

These scripts implement the sampling exhaustiveness test described in
[Viswanath et al, 2017](https://www.ncbi.nlm.nih.gov/pubmed/29211988).
The protocol is primarily designed to work with models generated by
the [Integrative Modeling Platform (IMP)](https://integrativemodeling.org),
but can probably be adapted for other systems.

## Dependencies:

[pyRMSD](https://github.com/salilab/pyRMSD) is needed. (This is a fork of the
original pyRMSD - which is no longer maintained - to fix bugs and add
Python 3 support.)

In the Sali lab, pyRMSD is already built, so can be used with
`module load python2/pyrmsd` or `module load python3/pyrmsd`.

## Usage:

For a full demonstration of the protocol, see its usage in
IMP's [actin modeling tutorial](https://integrativemodeling.org/tutorials/actin/analysis.html).

## Ambiguity:

The protocol can also handle systems with ambiguity (equivalent proteins,
e.g. multiple protein copies), where this information needs to be considered
while calculating the RMSD between models. The RMSD between two protein
models is the minimum RMSD over permutations of equivalent proteins.

For example, if a system has 2 copies of protein A and 1 copy of protein B, i.e. the proteins are A.0, A.1,B.0. The RMSD between any pair of models m0 and m1, is the minimum RMSD between `RMSD[m0(A.0,A.1,B.0) , m1(A.0,A.1,B.1)]` and `RMSD[m0(A.0,A.1,B.1), m1(A.1,A.0,B.1]`. Note that the copies of A in m1 were interchanged while calculating the second RMSD. 

To implement this, pyRMSD takes an additional argument `symm_groups` which is a list of particle indices of equivalent particles. For the above case for instance, `symm_groups` has one symmetric group with the particle indices of A.0 and A.1. `symm_groups=[[[A.0.b0,A.1.b0],[A.0.b1,A.1.b1],[A.0.b2,A.1.b2]..[A.0.bn,A.1.bn]]]`. Here `A.X.bi` is the index of the i'th bead in protein A.X and the ith beads of the two protein copies are considered equivalent particles. 

To generate this list of symmetric groups, one needs to pass an additional file with the ambiguity option to the master exhaust script. The file contains one line per symmetric group, and components of symmetric groups are separated by white space. See also the example in symminput. 
