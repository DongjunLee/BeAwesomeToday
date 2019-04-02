# PyTorch under the hood## Tensor

- [ATen](https://pytorch.org/cppdocs/#aten), which is the foundational tensor operation library on which all else is built;
- Zero-Copying
  - `_` Underline after an operation means **in-place** operation.
    - e.g.) `tensor.add_(1.0)`, `np_array += 1`
- Tensor Storage
  - can have multiple tensors sharing the **same storage**, but with different interpretations, also called **views**, but without duplicating memory
  - e.g.) ```a = torch.ones((2, 2)); b = tensor_a.view(4)``` a and b's storage is same
- Memory Allocators (CPU or GPU)
   

## JIT

- `torch.jit` and **TorchScript**
- `Eager` Mode (prototype) -> tracing, scripting -> `Script` Mode (deployment)
- Tracing
  - `torch.jit.trace(function, tensor)`
- Scripting
  - `@torch.jit.script` (decorator)
- TorchScript
  - The concept of having a well-defined **Intermediate Representation** (IR) is very powerful, it's the main concept behind LLVM platform as well;
    - To build the IR, PyTorch takes leverage of the Python **Abstract Syntax Tree** (AST)
  - Decouple the model (computational graph) from Python runtime
- JIT Phases
  - `AST` or `Code` -> Parsing -> Checking -> Optimization -> Translation -> Execution
- Serialization
  - `code/model.py`, `model.json`  

## Production- Prefer using good **binary serialization** methdos such as Protobuf that offers a **schema** and a schema evoluation mechanism.
- Be careful to avoid extra copies of your tensors, especially during pre-processing (use `in-place` operation)
- Use HTTP 2.0 if possible, and avoid the head-of-line blocking (e.g. `gRPC` use HTTP/2.0 and Protobuf)
- Batching data is a way to amortize the performance bottleneck.

## Reference

- [PyTorch under the hood](https://speakerdeck.com/perone/pytorch-under-the-hood) 


