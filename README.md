# 🔧 Simple DLL Injection 🔧
🔧 Simple DLL Injection into a Process with C++ 🔧

## 🔧 Inject DLL into Process (DLL Injection) 🔧

```
#include <iostream>
#include <Windows.h>

using namespace std;

int main()
{
	LPCSTR DllPath = "C:\\Simple-DLL-Injection\\C++\\Debug\\testlib.dll"; // The Path to our DLL
	
	HWND hwnd = FindWindowA(NULL, "Tutorial-x86_64"); // HWND (Windows window) by Window Name
	DWORD procID; // A 32-bit unsigned integer, DWORDS are mostly used to store Hexadecimal Addresses
	GetWindowThreadProcessId(hwnd, &procID); // Getting our Process ID, as an ex. like 000027AC
	HANDLE handle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, procID); // Opening the Process with All Access

	// Allocate memory for the dllpath in the target process, length of the path string + null terminator
	LPVOID pDllPath = VirtualAllocEx(handle, 0, strlen(DllPath) + 1, MEM_COMMIT, PAGE_READWRITE);

	// Write the path to the address of the memory we just allocated in the target process
	WriteProcessMemory(handle, pDllPath, (LPVOID)DllPath, strlen(DllPath) + 1, 0);

	// Create a Remote Thread in the target process which calls LoadLibraryA as our dllpath as an argument -> program loads our dll
	HANDLE hLoadThread = CreateRemoteThread(handle, 0, 0, 
	(LPTHREAD_START_ROUTINE)GetProcAddress(GetModuleHandleA("Kernel32.dll"), "LoadLibraryA"), pDllPath, 0, 0);

	WaitForSingleObject(hLoadThread, INFINITE); // Wait for the execution of our loader thread to finish

	cout << "Dll path allocated at: " << hex << pDllPath << endl;
	cin.get();

	VirtualFreeEx(handle, pDllPath, strlen(DllPath) + 1, MEM_RELEASE); // Free the memory allocated for our dll path

	return 0;
}
```

![Simple DLL Injection into a Process with C++](Images/DLLInjection.png)

![Binance Ready to give crypto a try ? buy bitcoin and other cryptocurrencies on binance](Images/binance.jpg)
