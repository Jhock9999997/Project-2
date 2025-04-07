# Project-2
The objective of this project was to analyze vulnerabilities in real-world software with the American fuzzy lop (AFL). 

**Objective**
The objective was to identify potential vulnerabilities within the chosen software and conduct an in-depth investigation into its code structure and behaviour under varied inputs. I selected the Tiny C Compiler (TCC), a lightweight and fast open-source C compiler, as my target. The decision to focus on TCC was driven by my familiarity with the C language, which I believe provided an advantage when creating the fuzzing harness and preparing input cases. 

**Fuzzing Process**


The first step of the process was to git clone the source. This is done so the source code repository is downloaded into your virtual machine and ready for use. Applying this step will also ensure I have access to the latest source code which is essential for the building and fuzzing of the software. Once downloaded, the source code is available for modification, compilation, and use in fuzzing and further development tasks (see picture below). 
![image](https://github.com/user-attachments/assets/c68bcea9-48e7-484a-8d04-26ffa9c79284)
I then began the building phase of the program, this was first done by navigating to the TinyCC directory through the CD function (cd tinycc). 


The next step was to compile the program with AFL instrumentation to prepare it for fuzzing. To enhance the detection of vulnerabilities, I decided to apply sanitizers.Since MemorySanitizer (Msan) cannot be used together with AddressSanitizer (Asan), I opted to use Asan along with UndefinedBehaviorSanitizer (UBSan). Asan helps in identyfing memory-related issues such as buffer overflow and use-after-free bugs. It also captures errors that might not cause immediate crashes but could lead to unpredictable behaviour. In comparison, UBSan focuses on identifying undefined behaviour such as integer overflow, incorrect type conversions, and misaligned pointers, which ensures more robust code by catching subtle errors
![image](https://github.com/user-attachments/assets/a4a7fdb9-2d36-4efd-aaf2-0a75012a14cb)

After the compilations has been prepared the “make” command will initiate the compilation and complete the task. 

After the compilation has been completed, the next focus was on creating a harness. This can be described as essentially a wrapper program around TinyCC that take inputs and compiles them using the software. The concept of this is to examine how the software handles different types of inputs, simulating a real-world usage while ensuring that the program can handle unexpected or malformed inputs correctly. For this software, I was able to create a simple harness with C language that I had learned through previous modules. My C program interacts with TinyCC by attaining an integer from the user and evaluating whether it is positive, negative or zero. I have attached a photo of the C program code for my harness below. 
![image](https://github.com/user-attachments/assets/ce0a782f-f598-426c-b1f4-037a448b759b)

I then created a series of input seeds. These seeds allow the fuzzer to mutate and begin to explore new code paths in TinyCC. For discovering new paths, I created four simple input files that would represent similar inputs that TinyCC may encounter. I have attached a screenshot of the inputs within each of the four seeds and explained what it is designed to do. 

**Input seed 1**
![image](https://github.com/user-attachments/assets/d332c284-9c10-4cb2-a0eb-e098a224d20e)
The code above displays a basic C program that contains a main function returning 0. The idea of this seed is to tests TinyCC’s ability to handle the simplest valid C program. 

**Input seed 2**
![image](https://github.com/user-attachments/assets/a1e34057-174f-4448-a425-436c12cd4b7f)
The code above can be seen as a baseline input, a small valid program that provides a starting point for the fuzzer to create variations and test how TinyCC processes and compiles simple code structures. 

**Input seed 3**
![image](https://github.com/user-attachments/assets/b48e3be1-a965-4d38-8803-c31321ee59d7)
The input of my third seed, a more complex input compared to the first two. It tests the software’s ability to handle user-defined functions, argument passing and returning values from functions.

**Input seed 4**
![image](https://github.com/user-attachments/assets/e277dc68-e6fa-4a86-9d5d-553731a2712e)
The fourth seed (picture above) is a simple C program that is usually used for complete beginners as it is a program that prints “Hello World” using the printf function. After printing the message, the program returns 0 to indicate the execution has been successful. This test TinyCC’s ability to handle output operations such as printing to the console. It is a standard first program in C with its purpose to test the software’s ability to correctly compile and execute a program with a standard library function basic syntax.


![image](https://github.com/user-attachments/assets/9165b189-68d8-4f30-8ca4-9774d86ad792)

Once the seeds have been created the next step is to apply the code that will begin fuzzing the software (see picture above). I did this with typing in: 
 	afl-fuzz -i in -d -o out ./simple @@
I added in the -d aspect as it enables deterministic fuzzing, where AFL tries predefined strategies to mutate inputs systematically. Compared to random fuzzing, this ensures each mutation covers a new possible path in the program. 

**Analysis**

I ran the AFL fuzzing session on TCC for 12 hours and 57 minutes, allowing for deep exploration and maximizing the chance of uncovering complex bugs. During this time, 48 full fuzzing cycles were completed, meaning AFL thoroughly mutated and tested my input corpus multiple times.

The session resulted in 161 unique corpus entries, each representing a distinct execution path through TCC. This shows that AFL effectively explored a wide variety of code paths, which is key to uncovering subtle vulnerabilities. Favored items were at 40.99%, indicating that a large portion of inputs triggered new or interesting behavior. These favored inputs help AFL prioritize mutations that are more likely to expose vulnerabilities. In addition, new edges were found at a rate of 46.58%, with 75 new branches discovered in total—demonstrating that nearly half of the possible code paths were exercised during the fuzz.

![image](https://github.com/user-attachments/assets/8e7c7af4-88be-4ddd-ae08-0df0b90a133c)


Although no crashes or hangs occurred, the 4.01 million total executions and the progress into stage 40/114 show that the fuzzer reached a deep testing phase. The combination of high coverage, numerous favored inputs, and newly discovered code paths suggests the fuzzing process was both thorough and effective.

Overall, this was a strong and comprehensive fuzzing run, offering high confidence in TCC's stability and resilience under varied inputs.




