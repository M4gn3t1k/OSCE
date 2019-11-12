Here I put the steps I'm doing to prepare XDS/OSCE certification. My time is limited so the process could be slow. The main thing is to get all the concepts clear and understandable.

<a href="https://www.sans.org/reading-room/whitepapers/malicious/basic-reverse-engineering-immunity-debugger-36982">Immunity Debugger basics </a><p>
<a href="https://www.ma.rhul.ac.uk/static/techrep/2009/RHUL-MA-2009-06.pdf">Buffer Overflow paper</a>
<a href=""></a>
<a href=""></a>
<a href=""></a>


<H1>Windows Shellcoding</H1>
- <b>Arwin.</b> To know address of functions from DLLs.<br>
- <b>Python27.</b> Add to environment variables "Path" c:\Python27<br>
- <b>bin2sc.py.</b> Script to extract hexadecimal opcodes from binary.
- <b>shellcodeTest.c.</b> C script to execute a shellcode.

<h2>WinAPI</h2>
WinAPI is a set of functions stored in default system DLLs. They allow users to use OS functionalities.

<b>What are DLLs?</b>
Dynamic Link Libraries, are files that contain functions. If we have a binary that makes use of these functions, the application needs to load that DLL to addresses in memory.

Is the same as programming languages: firstly you call the library to secondly call functions of that library.<p>

To know address of functions we can do it in many ways. A couple of them are:<br>

<ol><b><li>Immunity Debugger.</b> <b>Attach</b> the binary, <b>View</b> - <b>Executable Modules </b>- Select DLL we want, right click and <b>View names</b><br></li>
<b><li>Arwin.</b> c:\> arwin user32.dll MessageBoxA </li>
</ol>
  
<h2>How does all this work?</h2>
To call a function the process is:<p>
<ul>
<li>Load user32.dll library</li>
<li>Call MessageBoxA with its arguments</li>
</ul>
There is an important thing to say: the presence of ASLR (explained later) will change how all this works. If we are working in an OS where the addresses are static the things are much more easy than if ASLR is ON (in Linux systems we can bypass ASLR, but in Windows is more difficult).<p>
  
A library has its base address and each function is available at a static offset from the library base address.  

<h2>Getting our hands dirty...</h2>
Now we are going to create a shellcode from the scratch. An easy example to start with!<p>
We will launch a calc.exe application and will use WinExec() and ExitProcess() functions.<p>
To know addresses of both functions: <p>
  <ul><list>arwin kernel32.dll WinExec</list><p>
    <list>arwin kernel32.dll ExitProcess</list><p>
    </ul>
  
  <i>
  BITS 32<p>
  global _start<p>
  
  xor ebx, ebx<p>
  push ebx; null-termination<p>
  push 0x6578652e; "exe." in hex and endianness <p>
  push 0x636c6163: "clac" in hex and endianness<p>
  mov ecx, esp<p>
  push 1<p>
  push ecx<p>
  mov ebx, 0x77e484c6; push address of WinExec<p>
  call ebx; call WinExec()<p>
  
  </i>

  
