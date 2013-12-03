# CLFFT

[![Build Status](https://travis-ci.org/jakebolewski/CLFFT.jl.png)](https://travis-ci.org/jakebolewski/CLFFT.jl)

Julia bindings to AMD's clFFT library

## Example

```julia
import OpenCL
import CLFFT

const cl = OpenCL
const clfft = CLFFT

_, ctx, queue = cl.create_compute_context()

X = ones(Complex64, N)
bufX = cl.Buffer(Complex64, ctx, :copy, hostbuf=X)

p = clfft.Plan(Complex64, ctx, size(X))
clfft.set_layout(p, :interleaved, :interleaved)
clfft.set_result(p, :inplace)

clfft.enqueue_transform(p, :forward, [queue], bufX, nothing)  
cl.finish(queue)
    
result = cl.read(queue, bufX)
@assert isapprox(norm(result - fft(X)), zero(Float32)) => true
```
