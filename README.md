# Intel driver segfaults on OpArrayLength

```
Extended renderer info (GLX_MESA_query_renderer):
    Vendor: Intel Open Source Technology Center (0x8086)
    Device: Mesa DRI Intel(R) UHD Graphics 620 (WHL GT2) (0x3ea0)
    Version: 20.0.8
    Accelerated: yes
    Video memory: 3072MB
    Unified memory: yes
    Preferred profile: core (0x1)
    Max core profile version: 4.6
    Max compat profile version: 3.0
    Max GLES1 profile version: 1.1
    Max GLES[23] profile version: 3.2
```
```
$ make
$ ./segfault
Loading shader _Z9comp_mainv from length.spv
Segmentation fault (core dumped)
```

Intel driver segfaults in glLinkProgram when there is an OpArrayLength sourced on a StorageBuffer variable. This is generated with my own compiler. Maybe the emitted instructions are poisonously different from glslang's, but I can't really tell. Should not be segfaulting driver. Works fine with nvidia.


### Update:

[**segfault.comp**](segfault.comp)
```cpp
#version 460

layout(local_size_x=32) in;

layout(binding=0)
buffer MyBuffer1 {
	vec4 data0[];
};

layout(binding=1)
buffer MyBuffer2 {
	int len;
};

void main() {
	len = data0.length();
}
```

I wrote a GLSL one-liner to see if glslang-generated code has the same issue. It does. The example now uses glslc's generated segfault.spv.