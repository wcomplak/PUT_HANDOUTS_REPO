
Let's assume we need a function returning a floating-point value.\
In MS C/C++ x86 such values are returned always on the *FPU* stack. This makes things easier since the value is always 80b *extended*.
* * *
#### A function adding *float* and *int* returning *float*, computations on the *FPU*
its prototype would look like:
~~~
__declspec(naked) float __cdecl sum(float a, int b);
~~~
and its stack layout is:

| item | size | offset |
|:----|:----|:------|
| return address | 4b | esp + 0 |
| argument *a*   | 4b | esp + 4 |
| argument *b*   | 4b | esp + 8 |

~~~
__declspec(naked) float __cdecl sum(float a, int b)
{
	__asm
	{
		fld dword ptr [esp + 4]  ; load a
		fild dword ptr [esp + 8] ; load b
		faddp st(1), st(0)       ; add a and b
		ret                      ; and leave the result on the FPU stack
	}
}
~~~

#### A function adding *float* and *int* returning *float*, computations on the *SSE*
But if you want to perform computations using SSE returning value becomes complicated. Function prototype is without changes:
~~~
__declspec(naked) float __cdecl sum(float a, int b);
~~~
and stack layout is the same:

| item | size | offset |
|:----|:----|:------|
| return address | 4b | esp + 0 |
| argument *a*   | 4b | esp + 4 |
| argument *b*   | 4b | esp + 8 |

the key problem here is how to copy value from xmm register to FPU stack - there is no direct mov/load way. Of course there is CPU stack there:
~~~
__declspec(naked) float __cdecl sum(float a, int b)
{
	__asm
	{
		movss xmm0, [esp + 4]     ; load a
		cvtsi2ss xmm1, [esp + 8]  ; load+convert int->float b
		addss xmm0, xmm1          ; add a and b
        sub esp,4                 ; reserve memory
		movss [esp], xmm0         ; copy to CPU stack
		fld dword ptr [esp]       ; and load to FPU stack
		add esp,4                 ; free CPU stack
		ret                       ; leave the result on the FPU stack
	}
}
~~~



* * *
Wojciech Complak, Ph. D.  
[Institute of Computing Science](https://www.cs.put.poznan.pl)  
[Faculty of Computing and Telecommunications](https://cat.put.poznan.pl/)  
[Poznan Univ. of Technology](https://www.put.poznan.pl/)  