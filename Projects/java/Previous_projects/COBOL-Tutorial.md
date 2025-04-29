---
title: "COBOL Tutorial"
date: 2024-08-15T13:28:41Z
draft: false
---

# COBOL (Enterprise COBOL)

## 1. Code structure
```
|123456     |7        |891011  |1213141516...72 |73747576 ... 80
|           |         |        |                |
|Sequence   |         |        |                |
|number Area|         |        |                |
|           |Indicator|        |                | 
|           |         |A Area  |                | 
|           |         |        |B Area          |
|           |         |        |                |Identification Area
```

## 1.1 Sequence Number Area
Here's how sequence numbers are used in COBOL: \
1. Sequence numbers identify lines and assist in version control.
2. Found in columns 1-6 of COBOL source code.
3. Typically start at 000001, incrementing by 1 or 10.
4. Useful for line referencing and ordering in punched card systems.
5. Ignored by modern compilers, but maintained for legacy support.

## 1.2 Indicator

A character is placed in column 7 of the source code to instruct the compiler on how to process the line.

1. **\*** : *Comment Line*  
  Used to indicate a comment; the entire line is ignored by the compiler.

2. **-** : *Continuation Line*  
  Indicates that the current line is a continuation of the previous line.

3. **/ **: *Page Eject*  
  Forces a page break in the listing file, often used for formatting output.

4. **D** : *Debugging Line*  
  Marks a line of code that is only compiled when debugging is enabled.


## 1.3 The A Area
1. The A Area spans columns 8-11 in COBOL code.
2. Used for Division, Section, and Paragraph names.
3. Also holds level numbers and 01, 77, 66, 88 data definitions.
4. Key for starting Procedure Division statements.
5. Ensures proper alignment and readability in COBOL code.

## 1.4 The B Area
1. The B Area spans columns 12-72 in COBOL code.
2. Used for most COBOL statements and clauses.
3. Holds continuation of code from the A Area.
4. Allows for more complex logic and data definitions.
5. Ensures proper formatting and code structure.

## 1.5 The Identification Area
This is ignored by the compiler

1. The Identification Area spans columns 1-6.
2. Used for sequence numbers or line identification.
3. Helps in organizing and referencing code lines.
4. Important for legacy systems and code management.
5. Modern compilers typically ignore it, but it's kept for legacy support.

## 2. Cobol Reserved words
Here’s a list of popular reserved words in COBOL:

1. **ADD** – Adds values.
2. **SUBTRACT** – Subtracts values.
3. **MULTIPLY** – Multiplies values.
4. **DIVIDE** – Divides values.
5. **MOVE** – Moves data between fields.
6. **DISPLAY** – Outputs data to the screen.
7. **ACCEPT** – Inputs data from the user.
8. **IF** – Conditional statement.
9. **PERFORM** – Executes a paragraph or section.
10. **OPEN** – Opens a file.
11. **CLOSE** – Closes a file.
12. **READ** – Reads a record from a file.
13. **WRITE** – Writes a record to a file.
14. **STOP** – Stops program execution.
15. **SECTION** – Defines a section in the Procedure Division.
16. **EXIT** – Exits a program or a paragraph.
17. **INITIALIZE** – Sets fields to initial values.
18. **GOBACK** – Returns control to the calling program or operating system.
19. **CALL** – Invokes another program or subroutine.
20. **SET** – Sets values for switches or data items.
21. **SEARCH** – Searches a table.
22. **MERGE** – Merges sorted files or records.
23. **REWRITE** – Updates an existing record in a file.
24. **UNSTRING** – Splits a string into separate fields.
25. **STRING** – Concatenates strings.
36. **EVALUATE** – Performs conditional logic similar to a switch statement.

## 3. Cobal Structure Hierarchy
![Cobal Structure Hierarchy](http://rino.kozow.com/images/cobol.png)

Understanding how COBOL programs are organized will help you write and maintain your code more effectively. Here’s a simple breakdown of the structure:

1. **Division**: The highest level, dividing the program into major parts.
2. **Section**: Subsections within a Division to group related tasks.
3. **Paragraph**: Specific blocks of code within a Section.
4. **Sentence**: A set of COBOL statements that together perform a task.
5. **Statement**: Individual commands that perform specific actions.
6. **Data Division**: Where you define all the data your program will use.
7. **Procedure Division**: Where the actual operations of the program are written.

### 3.1 Division
- **What It Is**: The largest structural part of a COBOL program.
- **Why It Matters**: Divisions separate the program into different functional areas.
- **Types of Divisions**:
  - **Identification Division**: Contains program metadata (e.g., program name).
  - **Environment Division**: Describes the hardware and software environment.
  - **Data Division**: Defines all data elements and structures.
  - **Procedure Division**: Contains the instructions that the program executes.

**Example**:
```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SampleProgram.

ENVIRONMENT DIVISION.
...

DATA DIVISION.
...

PROCEDURE DIVISION.
...
```

### 3.2 Section
- **What It Is**: A smaller part within a Division, grouping related tasks or functionalities.
- **Why It Matters**: Helps in organizing code and improving readability.
- **When to Use**: Use Sections to organize your code logically, like separating input processing from output processing.

**Example**:
```cobol
PROCEDURE DIVISION.
INPUT-SECTION.
    ...

OUTPUT-SECTION.
    ...
```

### 3.3 Paragraph
- **What It Is**: A block of code within a Section that performs a specific task.
- **Why It Matters**: Breaks down Sections into manageable parts, each handling a distinct part of the logic.
- **When to Use**: Use Paragraphs to handle distinct operations like reading a file or calculating a value.

**Example**:
```cobol
INPUT-SECTION.
READ-DATA.
    READ CUSTOMER-FILE INTO CUSTOMER-RECORD.
    ...
```

### 3.4 Sentence
- **What It Is**: A sequence of COBOL statements that ends with a period (`.`).
- **Why It Matters**: Sentences define complete actions or operations, like moving data or performing calculations.
- **When to Use**: Use Sentences to group related statements that together accomplish a task.

**Example**:
```cobol
MOVE "John Doe" TO CUSTOMER-NAME.
DISPLAY CUSTOMER-NAME.
```

### 3.5 Statement
- **What It Is**: The smallest unit of code in COBOL, representing a single action.
- **Why It Matters**: Statements are the basic building blocks of COBOL programs.
- **Examples**:
  - **MOVE**: Transfers data from one place to another.
  - **ADD**: Performs addition.
  - **DISPLAY**: Outputs data to the screen.

**Example**:
```cobol
MOVE A TO B.
ADD 1 TO TOTAL.
DISPLAY "Total is: " TOTAL.
```

### 3.6 Data Division
- **What It Is**: The part of the program where you define all the data, such as variables, constants, and file structures.
- **Why It Matters**: All data that the program uses must be declared here before it can be used.
- **Key Sections**:
  - **Working-Storage Section**: For variables that hold data throughout the program.
  - **File Section**: For defining the structure of files the program will use.
  - **Linkage Section**: For data passed between different programs.

**Example**:
```cobol
DATA DIVISION.
WORKING-STORAGE SECTION.
01 CUSTOMER-NAME PIC X(50).

FILE SECTION.
FD CUSTOMER-FILE.
01 CUSTOMER-RECORD.
   05 CUSTOMER-ID     PIC 9(5).
   05 CUSTOMER-NAME   PIC X(50).
```

### 3.7 Procedure Division
- **What It Is**: The part of the program where you write the code that actually performs tasks.
- **Why It Matters**: This is where the logic and flow of the program are implemented.
- **How It Works**: The Procedure Division is organized into Sections and Paragraphs, making it easier to manage and understand the code.

**Example**:
```cobol
PROCEDURE DIVISION.
START-PROGRAM.
    DISPLAY "Program is starting...".
    PERFORM INPUT-SECTION.
    PERFORM PROCESS-DATA.
    PERFORM OUTPUT-SECTION.
    STOP RUN.
```

---

### 3.8 The Hello World example

Here’s a simple "Hello, World!" program in COBOL that demonstrates the basic structures:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. HelloWorld.

       DATA DIVISION.

       WORKING-STORAGE SECTION.
       01  WS-MESSAGE     PIC X(20) VALUE 'Hello, World!'.

       PROCEDURE DIVISION.
           DISPLAY WS-MESSAGE
           STOP RUN.
```

Using Sequence numbers
```
000010 IDENTIFICATION DIVISION.        
000020 PROGRAM-ID. HelloWorld.        

000030 DATA DIVISION.                 

000040 WORKING-STORAGE SECTION.        
000050 01  WS-MESSAGE     PIC X(20) VALUE 'Hello, World!'. 

000060 PROCEDURE DIVISION.           
000070 DISPLAY WS-MESSAGE            * Executes the DISPLAY statement
000080 STOP RUN.                     * Executes the STOP RUN statement


```

#### Breakdown of the Program:

**IDENTIFICATION DIVISION:**
- **PROGRAM-ID**: Specifies the name of the program (`HelloWorld`).

**DATA DIVISION:**
- **WORKING-STORAGE SECTION**: Defines a variable (`WS-MESSAGE`) to hold the message with a picture clause (`PIC X(20)`) indicating the variable can hold up to 20 characters.

**PROCEDURE DIVISION:**
- **DISPLAY**: Outputs the value of `WS-MESSAGE` to the screen.
- **STOP RUN**: Ends the program execution.


## 4. Cobol Variables


### 4.1 COBOL Variables and Naming Conventions

1. **Variable Definition:**
   - Variables in COBOL are defined in the **DATA DIVISION**.
   - Used to store data values that the program processes.

2. **Data Division Structure:**
   - **WORKING-STORAGE SECTION:** For variables that need to retain values throughout the program.
   - **FILE SECTION:** For file-related variables.
   - **LINKAGE SECTION:** For variables passed between programs.

3. **Variable Naming:**
   - **Level Numbers:** Define the hierarchy of data items (e.g., `01`, `05`, `77`).
   - **Data Names:** Should be meaningful and descriptive.

4. **Picture Clause (PIC):**
   - **Purpose:** Defines the data type and format of a variable.
   - **Syntax:** `PIC` followed by a description of the data type.
   - **Examples:**
     - `PIC X(n)`: Alphanumeric string of length `n` 255 is max n.
     - `PIC 9(n)V99`: Numeric value with `n` digits before the decimal point and 2 digits after. 18 is max n.

5. **Syntax Example:**
   ```cobol
   WORKING-STORAGE SECTION.
   01  CUSTOMER-NAME       PIC X(30).         * Alphanumeric, 30 characters
   01  TOTAL-AMOUNT         PIC 9(7)V99.      * Numeric, 7 digits before decimal, 2 after
   ```

---

### 4.2 Good Data Names

1. **Descriptive Names:**
   - **CUSTOMER-NAME**: Clearly indicates the data holds a customer's name.
   - **TOTAL-AMOUNT**: Specifies the variable is for storing a total amount.

2. **Consistent Naming Conventions:**
   - **Use Hyphens or Camel Case**: For readability (e.g., `CUSTOMER-NAME`, `TOTAL_AMOUNT`).
   - **Avoid Abbreviations**: Prefer full words for clarity (e.g., `TOTAL-TRANSACTION-AMOUNT` instead of `TOT-TRX-AMT`).

3. **Avoid Reserved Words:**
   - **Reserved Words**: Avoid using COBOL reserved words as variable names (e.g., `DISPLAY`, `MOVE`).

4. **Examples:**
   - **Correct**: `ORDER-QUANTITY`, `INVOICE-DATE`, `EMPLOYEE-ID`
   - **Incorrect**: `ORDQTY`, `INVDATE`, `EMPID`

5. **Best Practices:**
   - **Consistency**: Use a consistent naming pattern across the program.
   - **Clarity**: Ensure names are descriptive and make the code more readable.

---
  
### 4.3 Validating the SURNAME Variable
```
PERFORM VARYING I FROM 1 BY 1 UNTIL I > LENGTH OF SURNAME
    IF SURNAME(I:1) NOT IN 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'
        DISPLAY 'Invalid character found in SURNAME'
        EXIT PERFORM
    END-IF
END-PERFORM

```

---

## 5. Literals (COBOL Constants)
Literals in COBOL are fixed values that do not change during the execution of a program. These are constants.

### 5.1 Figurative Literals
Figurative literals are special reserved words that represent commonly used constants in COBOL. They make code more readable and concise.

1. **ZERO and ZEROS**  
   Represents the value `0`.  
   Example: `MOVE ZERO TO total.`

2. **SPACE and SPACES**  
   Represents one or more spaces (blanks).  
   Example: `MOVE SPACES TO customer-name.`

3. **LOW-VALUE**  
   Represents the lowest possible value in the collating sequence.  
   Example: `MOVE LOW-VALUE TO data-field.`

4. **HIGH-VALUE**  
   Represents the highest possible value in the collating sequence.  
   Example: `MOVE HIGH-VALUE TO end-of-file-marker.`

5. **NULL and NULLS**  
   Represents a null pointer or a memory reference that does not point to any object or value.  
   Example: `SET pointer TO NULL.`

## 6. The Data Division
All data you use in your COBOL program is declared and structured in the Data Division. This section defines the data items, their types, and their sizes.

### 6.1 Data Relationships
Data relationships define how different data items relate to each other in the program. For example, a `GROUP` item can hold multiple elementary items.

### 6.2 MOVE Statement
The `MOVE` statement transfers data from one variable to another.  
Example:  
```cobol
MOVE 100 TO total-sales.
MOVE customer-name TO display-name.
```

### 6.3 COMPUTE Statement
The `COMPUTE` statement is used to perform arithmetic calculations.  
Example:  
```cobol
COMPUTE total = price * quantity.
```

## 7. File Handling
File handling in COBOL involves reading from and writing to files. This section deals with how COBOL handles files.

### 7.1 The Environment Division
This section specifies the environment in which the program runs, including file assignments.  
Example:  
```cobol
SELECT input-file ASSIGN TO 'input.dat'.
```

### 7.2 The Data Division
This part specifies the structure of the records in the files used by the program.  
Example:  
```cobol
FD input-file.
01 input-record.
   05 customer-id     PIC X(5).
   05 customer-name   PIC X(30).
```

### 7.3 The Block Clause
In COBOL, you can group records into blocks to improve efficiency when handling large amounts of data.  
Example:  
```cobol
BLOCK CONTAINS 10 RECORDS.
```

## 8. The Procedure Division
The Procedure Division is where the logic of the COBOL program is written. It contains the executable instructions.

### 8.1 The PERFORM Statement
The `PERFORM` statement is used to execute a series of instructions or a paragraph multiple times. It is often used for looping.  
Example:  
```cobol
PERFORM 10 TIMES
   DISPLAY 'Hello, World!'.
END-PERFORM.
```

## 9. Programming Structure
COBOL programs are structured to ensure a clear flow of control. This section discusses different ways to structure your COBOL programs.

### 9.1 Using PERFORM to Run a Paragraph
A paragraph in COBOL is like a function in other languages. You use `PERFORM` to execute the code in a paragraph.  
Example:  
```cobol
PERFORM process-records.
```

### 9.2 Using PERFORM to Iterate a Loop
You can use `PERFORM` to loop until a certain condition is met.  
Example:  
```cobol
PERFORM UNTIL end-of-file
   READ input-file INTO input-record
   AT END
      SET end-of-file TO TRUE
   END-READ
   PERFORM process-record.
END-PERFORM.
```

### 9.3 The GOTO Keyword
The `GOTO` statement changes the flow of control by jumping to a different paragraph or section. However, it can make code difficult to follow and should be avoided when possible.

### 9.4 Paragraphs
Give paragraphs descriptive names to make the code easier to understand.  
Example:  
```cobol
PROCESS-RECORDS.
   PERFORM UNTIL end-of-file
      ...
   END-PERFORM.
```

### 9.5 PERFORM and TIMES Keyword to Iterate a Loop
Use `PERFORM ... TIMES` to execute a block of code a specific number of times.  
Example:  
```cobol
PERFORM 10 TIMES
   DISPLAY 'Processing...'.
END-PERFORM.
```

### 9.6 PERFORM THROUGH Keywords to Iterate a Sequence of Paragraphs
`PERFORM ... THROUGH` executes a range of paragraphs in sequence.  
Example:  
```cobol
PERFORM INIT-SECTION THROUGH END-SECTION.
```

## 10. Program Linkage
Program linkage involves calling other programs from within a COBOL program.

### 10.1 The CALL Keyword
The `CALL` statement is used to invoke another program.  
Example:  
```cobol
CALL 'subprogram-name' USING argument-1 argument-2.
```

### 10.2 The Linkage Section
The Linkage Section defines the parameters passed between programs.  
Example:  
```cobol
LINKAGE SECTION.
01 argument-1 PIC X(10).
01 argument-2 PIC 9(5).
```

### 10.3 Calling Programs Using the Linkage Section
Parameters passed via the Linkage Section can be used within the called program.

## 11. Generating Reports
COBOL is often used for generating structured reports.

### 11.1 Headers
Headers are used to display titles or column names at the top of a report.  
Example:  
```cobol
DISPLAY 'Customer Report'.
DISPLAY 'ID   Name     Balance'.
```

### 11.2 Fillers
Fillers are used to reserve space in a record but are not used for data storage.  
Example:  
```cobol
05 FILLER PIC X(10) VALUE SPACES.
```

### 11.3 Write Headers
You can use the `WRITE` statement to print headers in a report.  
Example:  
```cobol
WRITE report-line FROM report-header.
```

## 12. Conditional Expressions
Conditional expressions control the flow of execution based on certain conditions.

### 12.1 IF END-IF Statements
The `IF` statement is used to make decisions.  
Example:  
```cobol
IF total > 1000
   DISPLAY 'High sales'.
END-IF.
```

### 12.2 Conditional Names
Use an 88-level condition name to create meaningful names for values.  
Example:  
```cobol
01 status-code PIC 9(1).
   88 valid-code VALUE 1.
   88 invalid-code VALUE 0.
```

### 12.3 Conditional Operators
Operators like `=`, `>`, `<`, `>=`, `<=`, and `<>` are used in conditional expressions.  
Example:  
```cobol
IF total-sales > 1000
   ...
```

### 12.4 Conditional Expressions
Use `IF THEN ELSE` statements for conditional logic.  
Example:  
```cobol
IF balance < 0 THEN
   DISPLAY 'Negative balance'.
ELSE
   DISPLAY 'Balance OK'.
END-IF.
```

### 12.5 Evaluate Statements
The `EVALUATE` statement is similar to a `SWITCH` or `CASE` statement in other languages.  
Example:  
```cobol
EVALUATE true
   WHEN age < 18
      DISPLAY 'Minor'.
   WHEN age >= 18 AND age < 65
      DISPLAY 'Adult'.
   WHEN OTHER
      DISPLAY 'Senior'.
END-EVALUATE.
```

### 12.6 Search Statements
`SEARCH` is used to search through tables or arrays.  
Example:  
```cobol
SEARCH array-table
   AT END
      DISPLAY 'Not found'
   WHEN condition-met
      PERFORM process-item.
END-SEARCH.
```

### 12.7 Using Conditions to Test the Value of a Variable
COBOL provides various conditions to test the value of variables.
- `IS NUMERIC`
- `IS-NOT-ALPHABETIC-LOWER`
- `IS POSITIVE`
- `IS NEGATIVE`
- `IS ZERO`
- `IS NOT ZERO`

Example:  
```cobol
IF data-item IS NUMERIC
   DISPLAY 'Number'.
```

 
Certainly! Here’s the continuation:

---

## 13. Arithmetic Expressions (continued)

### 13.2 The Order COBOL Evaluates Expressions
COBOL follows PEMDAS (Parentheses, Exponents, Multiplication and Division, Addition and Subtraction) to evaluate arithmetic expressions. 

Example:
```cobol
COMPUTE result = (2 + 3) * 4 / 2 - 1.
```
This expression is evaluated as:
1. Add 2 and 3 to get 5.
2. Multiply 5 by 4 to get 20.
3. Divide 20 by 2 to get 10.
4. Subtract 1 from 10 to get the final result of 9.

## 14. Data Types
COBOL supports various data types, mainly categorized into numerical and alphanumeric types.

### 14.1 Numerical Types
Numerical data types are used for arithmetic operations.

#### 14.1.1 The Five Different Computational Representations

1. **COMP-1 (FLOATING POINT)**  
   Single precision floating-point number.  
   Example: `01 item COMP-1.`

2. **COMP-2 (DOUBLE PRECISION FLOATING POINT)**  
   Double precision floating-point number for more accuracy.  
   Example: `01 item COMP-2.`

3. **COMP-3 (BINARY CODED DECIMAL)**  
   Stores decimal numbers in binary-coded form, typically used for financial data.  
   Example: `01 item COMP-3 PIC 9(5)V99.`

4. **COMP-4 (BINARY INTEGER)**  
   Binary integer representation, often the default for binary data on many systems.  
   Example: `01 item COMP-4 PIC S9(4).`

5. **COMP-5**  
   Similar to COMP-4, but handles machine-specific optimizations.  
   Example: `01 item COMP-5 PIC S9(6).`

### 14.2 Alphanumeric Types
Alphanumeric types are used to store text data.

1. **EBCDIC (Extended Binary Coded Decimal Interchange Code)**  
   A character encoding standard mainly used in mainframes. It uses 8 bits for each character.  
   Example: `01 customer-name PIC X(30).`

2. **ASCII (American Standard Code for Information Interchange)**  
   Another character encoding standard, commonly used, especially in non-mainframe environments. It uses 7 bits for each character.  
   Example: `01 file-content PIC X(100).`

3. **UNICODE**  
   A character encoding standard that supports a wide range of characters from various languages, typically used in modern systems.  
   Example: `01 multilingual-text PIC N(50).`

## 15. Intrinsic Functions
COBOL provides a set of built-in functions, known as intrinsic functions, that perform various operations.

### 15.1 Categories of Intrinsic Functions
The main categories include:

1. **Mathematical Functions**  
   Example: `COMPUTE result = FUNCTION SUM(a b c).`

2. **Statistical Functions**  
   Example: `COMPUTE average = FUNCTION MEAN(a b c).`

3. **Date/Time Functions**  
   Example: `MOVE FUNCTION CURRENT-DATE(1:8) TO YYYYMMDD.`

4. **Financial Functions**  
   Example: `COMPUTE interest = FUNCTION PRESENT-VALUE(rate time).`

5. **Character-Handling Functions**  
   Example: `MOVE FUNCTION UPPER-CASE(customer-name) TO uppercase-name.`

6. **General Functions**  
   Example: `COMPUTE day-of-week = FUNCTION DAY-OF-WEEK(FUNCTION CURRENT-DATE).`

Example of using multiple functions together:
```cobol
COMPUTE due-date = FUNCTION DATE-OF-INTEGER(FUNCTION INTEGER-OF-DATE(YYYYMMDD) + 90).
DISPLAY 'Due Date: ' due-date.
```

---


## Bibliography
[COBOL Course - Programming with VSCode](https://www.youtube.com/watch?v=RdMAEdGvtLA&t=219s)

[COBOL Lesson 1 -- Introduction](https://www.youtube.com/watch?v=u9M52sAnrOs)

## Resources
[openmainframeproject/cobol-programming-course](https://github.com/openmainframeproject/cobol-programming-course)

[COBOL Lesson 1 -- Introduction](https://www.youtube.com/watch?v=u9M52sAnrOs)

## GNU Cobol
[https://gnucobol.sourceforge.io/](https://gnucobol.sourceforge.io/)
