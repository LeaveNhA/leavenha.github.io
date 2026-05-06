---
title: "How I Wrote a Jupyter Kernel for an Esoteric Language to Hand In My Homework"
date: 2026-05-06 01:42:00 +0300
categories:
  - Me
---

A few years ago, I had a machine learning assignment during my Master in Data Science. The assignment was *not* clear for me. I had rough times because of my background. I came from CS background, we don't give assignments without clear requirements where I came from. I asked; can I use any programming language? The answer was shocking for me; Yes!
So I decided to have some fun while doing my homework. Decided to write it in Doge. The problemo? The problemo revealed itself to me when I saw the submission requirements. It should be delivered in Jupyter Notebook format.

But Doge as a language, doesn't have any built-in tooling or support for extended environments like Jupyter. So, I had to write my own. Actually, I wrote it for all of us.

---

## The Kernel

Jupyter kernels speak a protocol. Five ZeroMQ sockets — shell, iopub, stdin, control, heartbeat — each with a specific role. The kernel receives `execute_request` on shell, runs the code, publishes results on iopub, and answers heartbeats to prove it's still alive.

I didn't want to implement that from scratch. Who would? So I leaned on `ipykernel`. The trick: Doge compiles to Python bytecode internally, running on the CPython VM. That meant I could subclass `IPythonKernel`, override `do_execute`, and let Doge handle the rest.

You can see the skeleton in the repo. `__init__.py` is two lines:

[\_\_init\_\_.py](https://github.com/LeaveNhA/idg/blob/master/idg/__init__.py)
```python
__version__ = '1.0'

from .idg import IdgKernel
```

A couple of lines to launches it:

[\_\_main\_\_.py](https://github.com/LeaveNhA/idg/blob/master/idg/__main__.py)
```python
"""__main__"""

from ipykernel.kernelapp import IPKernelApp
from . import IdgKernel

IPKernelApp.launch_instance(kernel_class=IdgKernel)
```


A couple of lines to tell the Jupyter how to start the kernel.

[kernel.json](https://github.com/LeaveNhA/idg/blob/master/idg/kernel.json)
```json
{
    "argv": [
        "python",
        "-m",
        "dg",
        "-m",
        "idg",
        "-f",
        "{connection_file}"
    ],
    "display_name": "Idg",
    "language": "dg"
}
```

The rest is simply the Kernel's protocol implementation for our backend. You can find it [here](https://github.com/LeaveNhA/idg/blob/master/idg/idg.dg). It can show images and draw plots too.

## After Math

It wasn't pretty. Actually it was, for me. It has bugs. The README admits: "Lots of, (bugs) I believe?" But it worked. I submitted assignment, and passed.

The instructor asked which language it was. Never told them it's an esoteric language. They just saw a clean notebook with code and results. I'm pretty sure that the syntax got them pretty hard and the matching output and shorter LoC based on Python implementation of the same assignment hit them harder.

## Why This Matters

I'm not the first person to write a Jupyter kernel for a niche language, and I won't be the last. But there's something liberating about refusing to let tooling dictate your workflow. If the ecosystem doesn't support your language, you bridge it. That's what programming is.


If you enjoy this kind of absurd problem-solving: I'm currently open for Clojure/ClojureScript consulting and small gigs. DM me on Clojurians Slack or find my contacts on GitHub. Crypto payments preferred.
