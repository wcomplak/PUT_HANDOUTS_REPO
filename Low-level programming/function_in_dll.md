now example of function in dll
~~~
#pragma warning(push, 4)
#include <stdlib.h>
#pragma warning( push )
#pragma warning( disable : 4100 )
__declspec(dllexport) __declspec(naked) void __stdcall InsertionSortAsm(int t[], int nelem)
{
  __asm
  {
	      cmp dword ptr [esp + 8], 1  ; nelem > 1
          jle retnow

	      mov edx, [esp + 4]          ; *t != NULL
	      test edx, edx
	      jz retnow

	      push ebx
	      push esi
	      ; push edi

          mov esi, 1                ; j = 1
    next: mov ecx, esi
          mov eax, [edx+ecx*4]      ; buf = t[j];
    iter: cmp eax, [edx+ecx*4-4]
          jge drop
          mov ebx, [edx+ecx*4-4]    ; t[j] = t[j - 1];
		  mov [edx+ecx*4], ebx
		  dec ecx           ; > 3 x faster than
		  jnz iter          ; loop  iter
    drop: mov [edx+ecx*4],eax
          inc esi
          cmp esi, [esp + 8 + 4 + 4]   ; +ebx +esi
          jl next

	      ; pop edi
	      pop esi
	      pop ebx
	retnow:
          ret 8
  }
}
#pragma warning( pop)
__declspec(dllexport) void __stdcall InsertionSortC(int t[], int nelem)
{
	if ((t == NULL) || (nelem < 2))return;
	for (int i = 1; i < nelem; i++)
	{
		int j = i;
		int buf = t[j];
		while ((j > 0) && (t[j - 1] > buf))
		{
			t[j] = t[j - 1];
			j--;
		}
		t[j] = buf;
	}
}
#pragma warning(pop)
~~~