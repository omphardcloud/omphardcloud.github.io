---
layout: page
title: HardCloud
permalink: /
---

The computing industry has recently proposed the use of  FPGAs as a way to improve performance and energy efficiency in modern cloud clusters. Unfortunately, using such FPGA clusters  is a very hard and complex task. In this context, we present HardCloud a novel and simple mechanism to offload computation to  the FPGAs available in the  Intel HARP2 platform. HardCloud extends OpenMP directives in such a way that the FPGA becomes just another OpenMP acceleration device that can be used directly from any user program. <!---
HardCloud is a subproject of [AClang](https://omp2ocl.github.io/aclang)).-->

## How it works

The version 4.0 of the  OpenMP standard introduces new directives that
enable the transfer of  computation to heterogeneous computing devices
(e.g.  GPUs  or  DSP).  We  use this  programming  model  to  transfer
computation to the HARP2 platform or to an HARP2 simulator (for debug purpose).
This can be done using two modes as described below: (1) Offloading a pre-synthesized
module; and (2) Using HardCloud to synthesize C and offload the resulting module.

### Offloading a pre-synthesized module
Here is a video demonstrating how to use the HardCloud for a pre-defined bitstream..

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/r5GFYUj2ajA?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

{% highlight C %}
#pragma omp target device(HARPSIM) map(to: A) map(from: B)
 #pragma omp parallel for use(hrw) module(loopback)
 // Code that represents the loopback bitstream
   for (int i = 0; i < NI; i++)
   {
     B[i] = A[i];
   }
 {% endhighlight %}

The example above shows the syntax that was adopted. As said in the video, the *map(:to)* clause indicates
the data that will be sent to the accelerator, while the *map(:from)* indicates the data that will be received from the accelerator as a result. The clause *use(hrw)* specifies that the annotated code block will use a pre-designed hardware, for example module (loopback), to do the computation instead of the C code following the annotation. The *device(HARPSIM)* clause indicates that the execution will be performed by the HARP2 simulator.
Optionally to HARPSIM, one can use the HARP device that instructs the
HardCloud to generate code for the real HARP instead of for the simulator. 

### Using HardCloud to synthesize C and offload the resulting module

Instead of using the *module* clause, to specify a pre-designed hardware module, a programmer can  use the HardCloud *synthesize* clause to generate a new bitstream starting from C code. For example, by using the synthesize clause in the following annotated code,  a C code  matrix multiplication  can be converted to OpenCL, followed to Verilog and finally synthesized as a hardware bitstream using the  Intel FPGA SDK for OpenCL. HardCloud takes the resulting bitstream, automatically  configures the HARP2 FPGA and finally runs the application.

{% highlight C %}
#pragma omp target device(HARP)
  #pragma omp target map(to: A[:N*N], B[:N*N]) map(from: C[:N*N])
  // Convert loop to OpenCL and then to  Verilog and synthesize
  #pragma omp parallel for use(hrw) synthesize(matmul)
  for(int i=0; i < N; ++i)
    for (int j = 0; j < N; ++j){
      C[i * N + j] = 0;
      for (int k = 0; k < N; ++k)
        C[i * N + j] += A[i * N + k] * B[k * N + j];
    }
{% endhighlight %}

This HardCloud operation mode is on-going work, and should be ready by late Feb. 2017.

<h2><font color="red">Open-souce release planned to 02/15/2018!</font></h2>

<!--- 
## The 1st HardCloud Workshop

*[HardCloud Tutorial](https://drive.google.com/file/d/0BwtIJD4CW5jfWlUyallEUXNWNGs3TW0wb21TdFJRY1REU2pv/view?usp=sharing).

 *[HardCloud Slides](https://drive.google.com/open?id=0BwtIJD4CW5jfY3V1M2V3cTUwazR6LTBURGdBdEZ3N2lEcWdJ).


 ## Documentation, Installation, Configuration

 All the information is provided [in the Wiki](https://github.com/omp2ocl/aclang/wiki).
 -->
