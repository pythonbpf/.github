<picture>
  <source
    media="(prefers-color-scheme: light)"
    srcset="https://github.com/user-attachments/assets/f3738131-d7cb-4b5c-8699-c7010295a159"
    alt="Light‐mode image">
  <img
    src="https://github.com/user-attachments/assets/b175bf39-23cb-475d-a6e1-7b5c99a1ed72"
    alt="Dark‐mode image">
</picture>

Welcome to the **Python-BPF Organization**, home to projects that enable writing, compiling, and running **eBPF programs entirely in Python**. Our tools allow Python developers to work directly with the Linux kernel’s eBPF infrastructure without writing C code, bridging the gap between high-level scripting and low-level system programming.

## Projects

### **[Python-BPF](https://github.com/pythonbpf/python-bpf)**

**Python-BPF** is an LLVM IR generator for eBPF programs written in Python. It converts Python code into LLVM Intermediate Representation (IR), compiles it into object files, and allows execution in the kernel.

* **Features**

  * Write eBPF programs using Python syntax.
  * Supports maps, helpers, tracepoints, and global definitions.
  * Generates LLVM IR using `llvmlite`.
  * Produces kernel-loadable ELF object files without embedding C code.
  * Includes debugging support for easier inspection.
* **Installation**

```bash
pip install pythonbpf pylibbpf
```

* **Example Usage**

```python
import time
from pythonbpf import bpf, map, section, bpfglobal, BPF
from pythonbpf.helpers import pid
from pythonbpf.maps import HashMap
from pylibbpf import BpfMap
from ctypes import c_void_p, c_int32, c_uint64
import matplotlib.pyplot as plt

@bpf
@map
def hist() -> HashMap:
    return HashMap(key=c_int32, value=c_uint64, max_entries=4096)

@section("tracepoint/syscalls/sys_enter_clone")
def hello(ctx: c_void_p) -> int:
    pid_val = pid()
    prev = hist().lookup(pid_val)
    hist().update(pid_val, (prev or 0) + 1)
    return 0

@bpf
@bpfglobal
def LICENSE() -> str:
    return "GPL"

b = BPF()
b.load_and_attach()
hist_map = BpfMap(b, hist)

time.sleep(10)
plt.hist(list(hist_map.values()), bins=20)
plt.show()
```

* **Learn More**

  * [Documentation & Examples](https://github.com/pythonbpf/python-bpf)
  * [Video Demo](https://youtu.be/eMyLW8iWbks)
  * [Slide Deck](https://docs.google.com/presentation/d/1DsWDIVrpJhM4RgOETO9VWqUtEHo3-c7XIWmNpi6sTSo/edit?usp=sharing)

---

### **[pylibbpf](https://github.com/pythonbpf/pylibbpf)**

**pylibbpf** provides Python bindings for `libbpf`, making it easy to load and attach eBPF programs from Python. It is designed to complement Python-BPF by handling kernel integration.

* **Features**

  * Load and attach eBPF programs directly from Python.
  * Works with maps, helpers, and program sections.
  * Built on `pybind11` for efficient C++ integration.
* **Installation**

```bash
sudo apt install libelf-dev
git clone --recursive https://github.com/varun-r-mallya/pylibbpf.git
pip install .
```

* **Usage**

  * Works seamlessly with Python-BPF-generated object files.
  * Requires `sudo` for Python interpreter when loading kernel programs.

## Community & Contributions

* Contributions are welcome! Please send PRs our way.
* Join discussions via Issues or Pull Requests.
* Stay updated with releases and deployments via GitHub.

---

## License

All projects in this organization are licensed under **Apache 2.0 / GPL** as specified per repository.
