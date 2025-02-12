# Custom Linux User-Space File Operations Application

## Overview
This application is a custom Linux user-space program that demonstrates basic file operations. It allows a user to input a string, save it to a source file, and optionally copy it to a destination file. If the user chooses not to copy the data, the contents of the source file will be displayed instead.

## Features
- Accepts user input and writes it to a source file (`source.txt`).
- Provides an option to copy the content to a destination file (`destination.txt`).
- Displays the content of the copied file if the transfer is completed.
- If the transfer is canceled, displays only the source file's content.
- Implements error handling for file operations.

## Prerequisites
Ensure you have the following:
- A Linux-based system.
- A working C compiler (e.g., `gcc`).
- Basic knowledge of file handling in C.

## Compilation
To compile the program, use the following command:
```sh
gcc -o file_operations file_operations.c
```
This will generate an executable named `file_operations`.

## Usage
To execute the program, run:
```sh
./file_operations
```
### Step-by-Step Execution
1. The program prompts the user to enter a string.
2. The string is written to `source.txt`.
3. The user is prompted with two options:
   - **Option 1:** Copy content from `source.txt` to `destination.txt`.
   - **Option 2:** Cancel the transfer and display the source file's content.
4. If option 1 is selected, `destination.txt` is created (or overwritten) with the contents of `source.txt`.
5. If option 2 is selected, the contents of `source.txt` are displayed, and no data transfer occurs.

## Example Output
### Example 1: Copy Operation
```
Enter the string to be written to the source file: Hello, Linux!
Data written to the source file.
Enter 1.To copy from source to destination 2.Cancel the Transmission(Display contents of source file alone)
1
Data from source file transferred to destination file.
Contents of the destination file:
Hello, Linux!
```
### Example 2: Cancel Operation
```
Enter the string to be written to the source file: Hello, Linux!
Data written to the source file.
Enter 1.To copy from source to destination 2.Cancel the Transmission(Display contents of source file alone)
2
Transfer from source to destination has been cancelled
Contents of the source file:
Hello, Linux!
```

## Error Handling
- If a file cannot be opened, an error message is displayed using `perror()`.
- If the destination file cannot be created or accessed, the program terminates after displaying an error.

## Cleanup
After execution, you may remove the generated files using:
```sh
rm source.txt destination.txt file_operations
```

## Additional Enhancements
To further improve the application, consider:
- Implementing support for handling larger files efficiently.
- Adding logging functionality to track file operations.
- Enhancing user input validation.
- Allowing file paths to be specified dynamically instead of hardcoding filenames.

## Conclusion
This application demonstrates the fundamental file operations in Linux user space, making it a great starting point for learning file handling in C. The optional transfer mechanism provides a simple way to practice interactive programming and error handling.

