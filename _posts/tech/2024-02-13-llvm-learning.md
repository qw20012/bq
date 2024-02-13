---
layout: post
title: LLVM Learning
category: tech
tags: LLVM
description: LLVM setup and demo
---

### What is the LLVM

The LLVM Project is a collection of modular and reusable compiler and toolchain technologies.

### Setup

We would install LLVM on Windows 11 

Firt, we need download the install the tools as below:

- [MinGW](https://github.com/niXman/mingw-builds-binaries/releases)
	I installed both 32bit and 64bit
	
- [LLVM-16.0.6-win64](https://github.com/llvm/llvm-project/releases/tag/llvmorg-16.0.6)
	This installer just includes the toolchain. So we need download the development lib in addition.
	
- [clang+llvm-16.0.6-x86_64-windows.tar.xz](https://github.com/awakecoding/llvm-prebuilt/releases)
	Copy bin, include and lib folder to LLVM folder after unzip.
	
### Demo
	
After setup, we would print "hello world" by creating 2 strings in different way.  The comments described the details. 
and I think you can catch it completely.	

    // Include the used lib files
	#include <llvm-c/Core.h>
	#include <llvm-c/ExecutionEngine.h>
	#include <llvm-c/Target.h>
	#include <llvm-c/Analysis.h>
	#include <llvm-c/BitWriter.h>
	#include <llvm-c/Transforms/PassManagerBuilder.h>

	#include <stdio.h>
	#include <stdlib.h>
	#include <stdbool.h>

	int main() {  
		// Create a new LLVM module and a builder
		LLVMModuleRef module = LLVMModuleCreateWithName("my_module");
		LLVMBuilderRef builder = LLVMCreateBuilder();
	 
		// Declare a string type in LLVM IR
		LLVMTypeRef stringType = LLVMPointerType(LLVMInt8Type(), 0);

		// Declare the printf function in LLVM
		LLVMTypeRef printfArgTypes[] = { stringType }; // printf takes char* as argument
		LLVMTypeRef printfReturnType = LLVMInt32Type(); // printf returns int
		LLVMTypeRef printfFuncType = LLVMFunctionType(printfReturnType, printfArgTypes, 1, 1); // isVarArg = true
		LLVMValueRef printfFunc = LLVMAddFunction(module, "printf", printfFuncType);

		// Create a local variable (pointer) in the entry block of a function
		LLVMTypeRef mainFuncType = LLVMFunctionType(LLVMVoidType(), NULL, 0, 0);
		LLVMValueRef mainFunc = LLVMAddFunction(module, "main", mainFuncType);
		LLVMBasicBlockRef entry = LLVMAppendBasicBlock(mainFunc, "entry");
		LLVMPositionBuilderAtEnd(builder, entry);
	 
		// Create a global string constant for "hello"
		LLVMValueRef helloStr = LLVMAddGlobal(module, LLVMArrayType(LLVMInt8Type(), 7), "helloStr");   
		LLVMSetInitializer(helloStr, LLVMConstString("hello", 6, 0));
		LLVMSetGlobalConstant(helloStr, 1);
		LLVMSetLinkage(helloStr, LLVMPrivateLinkage);

		// Allocate space for the pointer and store the address of the global string in it
		LLVMValueRef strPtr = LLVMBuildAlloca(builder, stringType, "strPtr");
		LLVMBuildStore(builder, LLVMBuildBitCast(builder, helloStr, stringType, ""), strPtr);
		
		// Declare a string variable in LLVM IR
		LLVMValueRef strVar = LLVMBuildAlloca(builder, stringType, "strVar");
	 
		// Assign the string "world" to the variable in LLVM IR
		LLVMValueRef helloString = LLVMBuildGlobalStringPtr(builder, "world", "helloString");
		LLVMBuildStore(builder, helloString, strVar);

		// Load the string pointer and pass it to printf
		LLVMValueRef loadedStrPtr = LLVMBuildLoad2(builder, LLVMTypeOf(strPtr), strPtr, "");
		LLVMValueRef loadedvariablePtr = LLVMBuildLoad2(builder, LLVMTypeOf(strVar), strVar, "");
		LLVMValueRef format = LLVMBuildGlobalStringPtr(builder,"%s %s.\n","format");
		
		// Call the printf function with arguments
		LLVMValueRef printfArgs[] = {format, loadedStrPtr, loadedvariablePtr};
		LLVMBuildCall2(builder, LLVMGlobalGetValueType(printfFunc), printfFunc, printfArgs, 3, "");
	 
		// Finish the function
		LLVMBuildRetVoid(builder);

		// Verify the module
		char *error = NULL;
		LLVMVerifyModule(module, LLVMPrintMessageAction, &error);
		LLVMDisposeMessage(error);

		// Print the LLVM IR to stdout
		LLVMDumpModule(module);

		// Print the LLVM IR to a local file
		LLVMPrintModuleToFile(module, "out_p1.ll", &error);

		// Compile IR file to exe file
		system("set PATH=%MINGW32%\bin;%PATH%");
		system("clang out_p1.ll -o out_p1.exe -lLLVM-C -target x86_64-pc-windows-gnu  -I \"C:\\tools\\llvm\\include\"  -L \"C:\\tools\\LLVM\\lib\"");
		
		// Initialize LLVM
		LLVMInitializeNativeTarget();
		LLVMInitializeNativeAsmPrinter();
		LLVMInitializeNativeAsmParser();

		// Create an execution engine
		error = NULL;
		LLVMExecutionEngineRef engine;
		LLVMCreateExecutionEngineForModule(&engine, module, &error);

		if (error) {
			fprintf(stderr, "Failed to create Execution Engine: %s\n", error);
			LLVMDisposeMessage(error);
			//return 1;
			exit(EXIT_FAILURE);
		}

		// Look up the main function using the execution engine
		void (*main)() = (void (*)())LLVMGetFunctionAddress(engine, "main");
		main(); // Call the function

		// Clean up
		LLVMDisposeBuilder(builder);
		LLVMDisposeModule(module);

		return 0;
	}

### Compile and Run

- Use 3 steps

Compile the C source code to LLVM IR using Clang
	
	clang -S -emit-llvm ir.c -o ir.ll -target x86_64-pc-windows-gnu -v -I "C:\tools\llvm\include"
	
Compile LLVM IR to object file using LLVM toolchain
	
	llvm-llc ir.ll -filetype=obj -o ir.o
	
Link object file with Clang and create the executable
	
    gcc ir.o -o ir.exe -I "C:\tools\llvm\include"  -L "C:\tools\LLVM\lib"  -lLLVM-C
	
Or link object file with MinGW and create the executable

	clang ir.o -o ir.exe -target x86_64-pc-windows-gnu  -I "C:\tools\llvm\include"  -L "C:\tools\LLVM\lib"  -lLLVM-C
	
- just use 2 commands with Clang

Compile the C source code to LLVM IR using Clang
	
	clang -S -emit-llvm ir.c -o ir.ll -target x86_64-pc-windows-gnu -v -I "C:\tools\llvm\include"
	
Compile LLVM IR and link to create the executable

	clang ir.ll -o ir.exe -lLLVM-C -target x86_64-pc-windows-gnu  -I "C:\tools\llvm\include"  -L "C:\tools\LLVM\lib"

- Or just use 1 command with MinGW

Compile and link to create the executable

	gcc p1.c -o p1 -I "C:\tools\llvm\include"  -L "C:\tools\LLVM\lib"  -lLLVM-C
	
