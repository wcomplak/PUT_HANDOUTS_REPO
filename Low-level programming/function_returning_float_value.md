
Let's assume we need a function returning a floating-point value.\
In MS C/C++ x86 such values are returned always on the *FPU* stack. This makes things easier since the value is always 80b *extended*.\

* * *
#### A function adding *float* and *int* returning *float*, computations on the *FPU*
its header would look like:
~~~
__declspec(naked) float __cdecl sum(float a, int b);
~~~
and its stack layout is:

| item | size | offset |
|:----|:----|:------|
| return address | 4b | esp + 0 |
| argument *a*   | 4b | esp + 4 |
| argument *b*   | 4b | esp + 4 |

~~~
__declspec(naked) float __cdecl sum(float a, int b)
{
	__asm
	{
		fld dword ptr[esp + 4]  ; a
		fild dword ptr[esp + 8] ; b
		faddp st(1), st(0)
		ret
	}
}
~~~


* * *




# PUT_HANDOUTS_REPO
* * *
Public resources primarily intended for PUT students (of various courses), but if you find something useful feel free to use.
* * *
## POSIX Threads (*pthreads*) for Windows

#### Compiled binaries of *pthreads* v2.11.0 and v3.0.0: 
(Pre)compiled binaries of pthreads *v2.11.0* and *v3.0.0* compiled with *cl 19.34.31935* (MS VS 2022).  
Each archive contains dlls, includes and libs in both 32b (subdirectory PTHREADS-BUILT.32) and 64b (subdirectory PTHREADS-BUILT.64) versions (include files are the same, dlls and libs share the same names however they are different):  
- [pthreads v2.11.0 binaries](https://github.com/wcomplak/PUT_HANDOUTS_REPO/tree/bfc026e5eb5219ddba950a98b2faead63a805490/pthreads/binary/pthreads4w-v2.11.0.zip)
- [pthreads v3.0.0 binaries](https://github.com/wcomplak/PUT_HANDOUTS_REPO/tree/bfc026e5eb5219ddba950a98b2faead63a805490/pthreads/binary/pthreads4w-v3.0.0.zip) 
* * *
Wojciech Complak, Ph. D.  
[Institute of Computing Science](https://www.cs.put.poznan.pl)  
[Faculty of Computing and Telecommunications](https://cat.put.poznan.pl/)  
[Poznan Univ. of Technology](https://www.put.poznan.pl/)  