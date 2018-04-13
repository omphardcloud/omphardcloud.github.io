---
layout: page
title: HardCloud
permalink: /
---

<p align="justify">
The computing industry has recently proposed the use of  FPGAs as a way to improve performance and energy efficiency in modern cloud clusters. Unfortunately, using such FPGA clusters  is a very hard and complex task. In this context, we present HardCloud a novel and simple mechanism to offload computation to  the FPGAs available in the  Intel HARP2 platform. HardCloud extends OpenMP directives in such a way that the FPGA becomes just another OpenMP acceleration device that can be used directly from any user program.
</p>

## How it works

<p align="justify">
The version 4.0 of the  OpenMP standard introduces new directives that
enable the transfer of  computation to heterogeneous computing devices
(e.g.  GPUs  or  DSP).  We  use this  programming  model  to  transfer
computation to the HARP2 platform or to an HARP2 simulator (for debug purpose).
This can be done using two modes as described below: (1) Offloading a pre-synthesized
module; and (2) Using HardCloud to synthesize C and offload the resulting module.
</p>

### Offloading a pre-synthesized module

{% highlight C %}
#pragma omp target device(HARPSIM) map(to: A) map(from: B)
#pragma omp parallel for use(hrw) module(loopback)
// Code that represents the loopback bitstream
for (int i = 0; i < NI; i++) {
    B[i] = A[i];
}
{% endhighlight %}

<p align="justify">
The example above shows the syntax that was adopted. The <b>map(:to)</b> clause indicates
the data that will be sent to the accelerator, while the <b>map(:from)</b> indicates the data that will be received from the accelerator as a result. The clause <b>use(hrw)</b> specifies that the annotated code block will use a pre-designed hardware, for example module (loopback), to do the computation instead of the C code following the annotation. The <b>device(HARPSIM)</b> clause indicates that the execution will be performed by the HARP2 simulator.
Optionally to HARPSIM, one can use the HARP device that instructs the
HardCloud to generate code for the real HARP instead of for the simulator.
</p>

### Using HardCloud to synthesize C and offload the resulting module

<p align="justify">
[Not available in the curret release]
</p>

<p align="justify">
Instead of using the <b>module</b> clause, to specify a pre-designed hardware module, a programmer can  use the HardCloud <b>synthesize</b> clause to generate a new bitstream starting from C code. For example, by using the synthesize clause in the following annotated code,  a C code  matrix multiplication  can be converted to OpenCL, followed to Verilog and finally synthesized as a hardware bitstream using the  Intel FPGA SDK for OpenCL. HardCloud takes the resulting bitstream, automatically  configures the HARP2 FPGA and finally runs the application.
</p>

{% highlight C %}
#pragma omp target device(HARP)
#pragma omp target map(to: A[:N*N], B[:N*N]) map(from: C[:N*N])
// Convert loop to OpenCL and then to  Verilog and synthesize
#pragma omp parallel for use(hrw) synthesize(matmul)
for(int i=0; i < N; ++i) {
    for (int j = 0; j < N; ++j) {
        C[i * N + j] = 0;
        
        for (int k = 0; k < N; ++k) {
            C[i * N + j] += A[i * N + k] * B[k * N + j];
        }
    }
}
{% endhighlight %}

## Documentation, Installation, Configuration

<p align="justify">
All the information is provided in the <a href="https://github.com/omphardcloud/hardcloud/wiki">Wiki</a>.
</p>

<h2><font color="red">Open-souce release planned to 04/20/2018!</font></h2>
