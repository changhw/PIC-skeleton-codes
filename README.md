# Particle-In-Cell Skeleton Codes
Succinct PIC Implementation 

__All source code in this repository is confidential.__

If you're viewing this and don't have explicit permission from the UCLA Plasma Physics Simulation Group to access this repository please contact Warren Mori (mori@physics.ucla.edu).

## Quick Start

Welcome! There is a large collection of PIC codes here with a range of complexity. They are all designed to be easy to read, understand, and adapt. 

1. The main directories are organized by the method of parallel programming technique.

2. Each sub-directory contains a complete, standalone PIC implementation.

3. *Where to Start*? The directory `serial` contains the basic, classic, serial Particle-In-Cell codes. For those new to PIC it is the perfect place to start... no bells or whistles... just a simple way to see how PIC codes work. For people already experienced with PIC, `serial` provides electrostatic, electromagnetic, and Darwin codes in 1D, 2D, and 3D that can be used in benchmarking or as unit-testing scaffolding. 

4. To see the range of available codes, check out the [Directory Overviews](#overview-of-codes-directories) and [Code Table](#whats-what) sections.

3. These codes have no external dependecies - You should be able to set the basic configuration and then `make`. For full instructions, see the readme files in each sub-directory.

## Introduction

Skeleton codes are bare-bones but fully functional PIC codes containing all the crucial elements but not the diagnostics and initial conditions typical of production codes. These are sometimes also called mini-apps. We are providing a hierarchy of skeleton PIC codes, from very basic serial codes for beginning students to very sophisticated codes with 3 levels of parallelism for HPC experts. The codes are designed for high performance, but not at the expense of code obscurity.  They illustrate the use of a variety of parallel programming techniques, such as MPI, OpenMP, and CUDA, in both Fortran and C.  For students new to parallel processing, we also provide some Tutorial and Quickstart codes.

The codes are available for download on the subpages here, on https://idre.ucla.edu/hpc/parallel-plasma-pic-codes, and on the website for the Particle-in-Cell and Kinetic Simulation Software Center at UCLA (http://picksc.idre.ucla.edu/software/skeleton-code/).

These codes have multiple purposes. The first is educational, by providing example codes for computational physics researchers of what a PIC code is and how to parallelize it with different parallel strategies and languages. The second is to provide benchmark codes for computer science researchers to evaluate new computer hardware and software with a non-trivial computation. Third, for researchers with already existing codes, these skeleton codes can be mined for useful ideas and procedures that can be incorporated into their own codes. Finally, the skeleton codes can be expanded and customized by adding diagnostics, initial conditions, and so forth, to turn them into production codes.

## Types of available PIC codes

There are a variety of PIC codes in common use. They differ in the forces used, time scales supported, and physical and numerical approximations used. We currently provide skeleton codes for 3 different types of PIC codes. The electromagnetic codes contain the most complete physics, using the full set of Maxwell’s equations and relativity. The highest frequency waves in the model are light waves and this model requires the shortest time step to resolve them. The Darwin model uses the Darwin subset of Maxwell’s equations which neglects the transverse displacement current in Ampere’s law. This approximation permits transverse electric and magnetic fields generated by plasma currents but neglects retardation and light waves. The highest frequency modes in this model are plasma waves (or upper hybrid waves if an external magnetic field is present). Darwin models are sometimes called magneto-static, near-field. or radiation-less electromagnetic models. The time step used can be much larger than that used by the electromagnetic codes, so the codes can run faster. The electrostatic codes retain only the longitudinal electric field from Poisson’s equation and neglects all transverse electric and magnetic fields. The highest frequency and time step used is the same as in the Darwin code, but the code is much simpler and executes faster. Which code is appropriate depends on the problem being studied. Descriptions of the mathematics behind all three models are available on the PICKSC website as a PDF (look under Notes for "[Description of Spectral Particle-in-Cell Codes from the UPIC Framework](http://picksc.idre.ucla.edu/publications/publications-reports-and-notes/)"). Other types of skeleton codes will be developed in the future and we solicit contributions.

There are examples for 1, 2, and 3 dimensional codes.  For simplicity, all codes are designed for uniform plasmas.   They are divided into layers with increasing levels of parallelism. There are three types of codes in each layer, simple electrostatic codes as well as more complex electromagnetic and Darwin codes. All codes use the same algorithms, so if one’s interest is primarily in the algorithms, the electrostatic codes are recommended. The codes are executing the same calculation for each level of parallelism, but the execution is done differently. Each level of parallelism incrementally increases the complexity, and enables the program to execute faster. This hierarchy of codes could be used in a semester course on parallel PIC algorithms, so that a beginning student could become an HPC expert by the end. Spectral field solvers based on Fast Fourier Transforms (FFTs) are used, but the main focus of these skeleton codes is on processing particles, which normally dominates the CPU time.

## Software Design

Most of the 2D codes are written in two languages, Fortran and C. The 1- and 3D codes do not have C procedure libraries, but they do have C main programs that can call the Fortran procedure libraries.  Additionally, some of the codes can be run interactively with an included Python script. Language interoperability is also maintained, so that Fortran codes can call C libraries and vice-versa. Fortran2003 has built-in support for interoperability with C. C procedures can be called by simply writing a file containing interface statements (similar to a header file in C). To allow C to call Fortran procedures, a simple wrapper library must be built in Fortran to allow interoperability with Fortran arrays (which is functionally similar to array objects in C++). Interoperability is important for a number of reasons. The first is to give Fortran access to features available only in C, for example, to permit a Fortran program to call a CUDA C procedure on a GPU. The second is to allow a C program access to procedures already available in Fortran. Our goal is to support multi-lingual codes, where researchers write in whatever language they are fluent in, and the pieces can be integrated seamlessly.   Because the features of Fortran2003 are not yet well known in the scientific community, an older style of interoperability is currently provided in most cases.   The details of this approach are discussed in Ref. [[1](#ref1)].   Most of the libraries in the skeleton codes have interface functions to make them interoperable.

The computationally intensive procedures in Fortran are written in a Fortran77 style subset of Fortran90. This is primarily because programs written in such a style tend to execute faster. But an additional reason is that Fortran has a large legacy of codes written in this style and it is useful that students be aware of such codes. All libraries written in such a style have a corresponding interface library to enable type checking when used with Fortran90. The C codes adhere to the C99 standard, primarily to make use of float complex types.

Most of the codes maintain a simple two level hierarchy, with a main code and an encapsulated library. In a some cases, additional layers are needed. One example is a library which makes use of MPI communications. In such a case, the MPI features are usually completely contained within the library and the MPI details (or MPI header files) are not exposed to other layers. This kind of isolation more easily enables integrating different programming layers, such as GPU and MPI programming.

A flat data structure is used, using only basic integer and floating point types, without the use of derived types or structs. This is primarily to expose as much as possible without hiding features in a multi-layered hierarchy. We believe this makes it easier to understand the changes made when increasing the levels of parallelism. But another reason is that the PIC community has not yet agreed on standard objects and it would be more difficult to reuse software from the skeleton codes if the types or structs we chose conflicted.

<a name="overview-of-codes-directories"/>
## Overview of Code Directories

### QuickStart

These example codes show how to perform a simple parallel addition of two arrays using different parallel programming paradigms.  They are primarily intended to illustrate how to startup, run and shutdown a simple parallel code and how to compile and link it.  Examples are in both C and Fortran.

### Serial

The basic serial codes do not make use of any parallelism, and are the base codes for students or researchers who are unfamiliar with PIC codes.  

### OpenMP

These codes illustrate how to use shared memory parallelism with OpenMP.  The algorithm used here is the same as that used on the GPU: it divides particles into small 2d tiles and reorders them every time step to avoid data collisions when performing the deposit.  Each tile is controlled by a single thread.  The algorithm is described in detail in Ref. [[4](#ref4)].

### Vectorization 

These codes illustrate how to use vectorization with the Intel Processors. Two approaches are illustrated. One uses the Intel SSE2 vector intrinsics, which is a low level data parallel language closely related to the native assembly instructions. This gives the best performance but requires substantial effort and expertise. The other approach uses compiler directives and often requires reorganization of the data structures and loops, but is much simpler.

### MPI 

These codes illustrate how to use domain decomposition with message-passing (MPI).  This is the dominant programming paradigm for PIC codes today.  These codes use only a simple 1d domain decomposition.  The algorithm is described in detail in Refs. [[2-3](#ref2)].  


### OpenMP/MPI

These codes illustrate how to use a hybrid shared/distributed memory algorithm, with a tiled scheme on each shared memory multi-core node implemented with OpenMP, and domain decomposition connecting such nodes implemented with MPI.  The algorithms are described in detail in Refs. [[2-4](#ref2)].

### OpenMP/Vectorization 

These codes illustrate how to use hybrid shared memory/vectorization algorithm, with a tiled scheme on each shared memory multi-core node implemented wit OpenMP and vectorization implemented with both SSE vector intrinsics and compiler vectorization. The tiling scheme is described in detail in Ref.[[4](#ref4)]. 

### GPU Codes

These codes illustrate how to implement an optimal PIC algorithm on a single GPU as well as on multiple GPUs.  Both CUDA C and CUDA Fortran interoperable versions are available, where a Fortran code can call the CUDA C libraries and a C code can call the CUDA Fortran libraries.  For two-level-parallelism, the codes use a hybrid tiling scheme with SIMD vectorization, both written with NVIDIA’s CUDA programming environment.  The tiling algorithm used within a thread block on the GPU is the same as that used with OpenMP [[4](#ref4)].  Unlike the OpenMP implementation, however, each tile is controlled by a block of threads rather than a single thread, which requires a vectorized or data parallel implementation.  For three-level-parallelism, the codes use a hybrid tiling scheme with SIMD vectorization on each GPU, and domain decomposition connecting such GPUs implemented with MPI.  The algorithms used are described in Refs. [[2-4](#ref2)].

<a name="whats-what"/>
## What's What

Here's a table showing the names of the codes and their parallelism (ES means electrostatic, EM means electomagnetic, D means Darwin)

| Category | directory name        | Field Type  | Language  | Parallelism |
| -------- | --------------------- | ----------- | --------- | ----------- |
|__Basic Serial__|
||pic1|ES|C,Fortran| None-Serial|
||pic2|ES|C,Fortran| None-Serial|
||pic3|ES|C,Fortran| None-Serial|
||bpic1|EM|C,Fortran| None-Serial|
||bpic2|EM|C,Fortran| None-Serial|
||bpic3|EM|C,Fortran| None-Serial|
||dpic1|D|C,Fortran| None-Serial|
||dpic2|D|C,Fortran| None-Serial|
||dpic3|D|C,Fortran| None-Serial|
|__1 Level Parallel__|
||mpic1|ES|C,Fortran|OpenMP|
||mpic2|ES|C,Fortran|OpenMP|
||mpic3|ES|C,Fortran|OpenMP|
||mbpic1|EM|C,Fortran|OpenMP|
||mbpic2|EM|C,Fortran|OpenMP|
||mbpic3|EM|C,Fortran|OpenMP|
||mdpic1|D|C,Fortran|OpenMP|
||mdpic2|D|C,Fortran|OpenMP|
||mdpic3|D|C,Fortran|OpenMP|
||ppic2|ES|C,Fortran|MPI|
||pbpic2|EM|C,Fortran|MPI|
||pdpic2|D|C,Fortran|MPI|
||vpic2|ES|C,Fortran|SSE|
||vbpic2|EM|C,Fortran|SSE|
|__2 Level Parallel__|
||gpupic2|ES|C,Fortran|CUDA(C)+Vectorization|
||gpubpic2|EM|C,Fortran|CUDA(C)+Vectorization|
||gpufpic2|ES|C,Fortran|CUDA(Fortran)+Vectorization|
||gpufbpic2|EM|C,Fortran|CUDA(Fortran)+Vectorization|
||mppic2|ES|C,Fortran|OpenMP+MPI|
||mpbpic2|EM|C,Fortran|OpenMP+MPI|
||mpdpic2|D|C,Fortran|OpenMP+MPI|
||vmpic2|ES|C,Fortran|OpenMP+SSE|
||vmpic3|ES|C,Fortran|OpenMP+SSE|
||vmbpic2|EM|C,Fortran|OpenMP+SEE|
|__3 Level Parallel__|
||gpuppic2|ES|C,Fortran|MPI+CUDA(C)+Vectorization|
||gpupbpic2|EM|C,Fortran|MPI+CUDA(C)+Vectorization|
||gpufppic2|ES|C,Fortran|MPI+CUDA(Fortran)+Vectorization|
||gpufpbpic2|EM|C,Fortran|MPI+CUDA(Fortran)+Vectorization|



##Future Work

There are 3 different topics we plan to study. One is to create advanced versions of some of these skeleton codes to include features such as dynamic load balancing. Another is to implement additional 3D versions of some of the skeleton codes. Finally, we plan to explore alternative programming paradigms, such as OpenCL, OpenACC, or co-array Fortran, perhaps with other partners.

##Support

This work is supported in part by the National Science Foundation, Department of Energy SciDAC program and the UCLA Institute for Digital Research and Innovation (IDRE). It is also closely co-ordinated with the activities of the NSF funded Particle-in-Cell and Kinetic Simulation Center (PICKSC) at UCLA (http://picksc.idre.ucla.edu/).



##References

<a name="ref1"/>
[1] Viktor K. Decyk, "A Method for Passing Data Between C and Opaque Fortran90 Pointers," ACM Fortran Forum, vol. 27, no. 2, p. 2 (2008). [doi link](http://dx.doi.org/10.1145/1408643.1408644)

<a name="ref2"/>
[2] P. C. Liewer and V. K. Decyk, “A General Concurrent Algorithm for Plasma Particle-in-Cell Codes,” J. Computational Phys. 85, 302 (1989). [doi link](http://dx.doi.org/10.1016/0021-9991(89)90153-8)

<a name="ref3"/>
[3] V. K. Decyk, "Skeleton PIC Codes for Parallel Computers," Computer Physics Communications, 87, 87 (1995). [doi link](http://dx.doi.org/10.1016/0010-4655(94)00169-3)

<a name="ref4"/>
[4] Viktor K. Decyk and Tajendra V. Singh, “Particle-in-Cell algorithms for emerging computer architectures,” Computer Physics Communications 185, 708 (2014). [doi link (free access)](http://dx.doi.org/10.1016/j.cpc.2013.10.013)
