# Linux内核编程

## AT&T汇编

```
1. 寄存器引用
引用寄存器要在寄存器前加%，如mov %eax, %ebx

2. 操作数顺序
操作数排列是从源（左）到目的（右），如mov %eax(源)， %ebx(目的)

3. 常数/立即数的格式
使用立即数，要在数前面加$，如mov $4, %ebx
符号常数直接引用，如move value, %ebx
引用符号地址在符号前加$，如mov $value, %ebx

4. 操作数的长度
操作数的长度用加在指令后的符号表示
b(byte), w(word), l(long)，如movw %ax, %bx

5. 在AT&T汇编格式中，绝对转移和调用指令（jmp/call）的操作数前要加上“*”作为前缀

6. 远转移指令和远调用指令的操作码在AT&T中为“ljump”和“lcall”，而在Intel汇编格式中为“jmp far”和“call far”
AT&T: 
		ljump $section, $offset
		lcall $section, $offset
		
7. 远程返回指令
AT&T: lret $stack_adjust
Intel:  ret far stack_adjust

8. 寻址方式
计算方法是base+index*scale+disp
AT&T: section:disp(base, index, scale)					
Intel: section:[base+index*scale+disp]

		AT&T																			ntel
movl -4(%ebp), %eax											mov eax, [ebp - 4]
movel array(, %eax, 4), %eax						  mov eax, [eax*4 + array]
movw array(%ebx, %eax, 4), %cx 				   mov cx, [ebx+4*eax+array]
movb $4, %fs:(%eax)											 mov fs:eax, 4

9. c嵌入汇编
格式：
GNU c:	__asm__("asm statement":outputs:inputs:registers-modified);
示例：
	_asm_("pushl %%eax \n\t""movl $0, %%eax \n\t" "popl %eax");
	{
		register char_res;\
		asm("push %%fs\n\t"
		"movw %%ax, %%fs\n\t"
		"pop %%fs":"=a(_res):"0"(seg), "m"(*(addr)));\
		_res;
	}
	
int main() 
{
		int a1 = 10, b1 = 0;
		asm("movl %1, %%eax;\n\r"
		"movl %%eax, %%ecx;"
		:"=a"(b1)
		:"b"(a1));
		
		printf("Result: %d, %d\n", a1, b1);
}

"a", "b", "c", "d"分别表示寄存器eax,ebx,ecx和edx
"S"和"D" 	寄存器esi,edi
"r"		任何寄存器
"0"
```

