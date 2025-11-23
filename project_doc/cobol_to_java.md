# COBOL to Java Mapping Guide

This document provides a comprehensive guide for understanding how COBOL concepts in CobolCraft map to equivalent Java concepts, particularly relevant for developers familiar with Java/Minecraft development who are learning COBOL.

## Table of Contents

1. [COBOL Programming Basics](#cobol-programming-basics)
2. [COBOL Language Features](#cobol-language-features)
3. [Systems Built on COBOL](#systems-built-on-cobol)
4. [COBOL Deficiencies Compared to Java](#cobol-deficiencies-compared-to-java)
5. [Basic Concepts](#basic-concepts)
6. [Programs vs Classes and Methods](#programs-vs-classes-and-methods)
7. [Data Structures](#data-structures)
8. [Inheritance and Polymorphism](#inheritance-and-polymorphism)
9. [Callbacks and Interfaces](#callbacks-and-interfaces)
10. [Memory Management](#memory-management)
11. [Error Handling](#error-handling)
12. [Common Patterns](#common-patterns)
13. [Practical Examples](#practical-examples)

## COBOL Programming Basics

### What is COBOL?

COBOL (Common Business-Oriented Language) is a high-level programming language designed in 1959 for business, finance, and administrative systems. It was created to be readable, portable, and suitable for processing large volumes of data.

### COBOL Program Structure

Every COBOL program follows a strict four-division structure:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. HelloWorld.
AUTHOR. Developer Name.
DATE-WRITTEN. 2024-01-01.

ENVIRONMENT DIVISION.
CONFIGURATION SECTION.
SOURCE-COMPUTER. IBM-PC.
OBJECT-COMPUTER. IBM-PC.

DATA DIVISION.
WORKING-STORAGE SECTION.
    01 GREETING                PIC X(12) VALUE "Hello World".

PROCEDURE DIVISION.
    DISPLAY GREETING
    STOP RUN.
END PROGRAM HelloWorld.
```

**The Four Divisions:**

1. **IDENTIFICATION DIVISION**: Program metadata (name, author, date, etc.)
2. **ENVIRONMENT DIVISION**: Hardware/software dependencies (file connections, I/O)
3. **DATA DIVISION**: All data definitions
   - `WORKING-STORAGE SECTION`: Local variables
   - `LINKAGE SECTION`: Parameters passed to/from programs
   - `FILE SECTION`: File record definitions
4. **PROCEDURE DIVISION**: Executable code (the actual program logic)

### Key COBOL Concepts

#### Data Types and Picture Clauses

COBOL uses "PICTURE" clauses (abbreviated as `PIC`) to define data formats:

```cobol
01 CUSTOMER-NAME           PIC X(50).        *> Alphanumeric string (50 chars)
01 CUSTOMER-ID             PIC 9(10).        *> Numeric (10 digits)
01 BALANCE                 PIC S9(10)V99.    *> Signed numeric with 2 decimals
01 IS-ACTIVE               PIC X.            *> Single character (Y/N flag)
01 AMOUNT                  PIC $$$,$$$,$$9.99. *> Formatted currency
```

**Common Picture Clauses:**
- `X`: Alphanumeric character
- `9`: Numeric digit
- `S`: Sign indicator
- `V`: Implicit decimal point
- `$`, `,`, `.`: Display formatting characters

#### Level Numbers

COBOL uses hierarchical level numbers to define data structures:

```cobol
01 CUSTOMER-RECORD.              *> Level 01 = top-level group
    05 CUSTOMER-ID            PIC 9(10).
    05 CUSTOMER-NAME.
        10 FIRST-NAME         PIC X(30).    *> Level 10 = nested
        10 LAST-NAME          PIC X(30).
    05 ADDRESS.
        10 STREET             PIC X(50).
        10 CITY               PIC X(30).
        10 ZIP-CODE          PIC 9(5).
```

**Level Number Rules:**
- `01` or `77`: Top-level items
- `02-49`: Group items (can contain sub-items)
- `66`: Renames clause (alternative view of data)
- `77`: Independent elementary item
- `88`: Condition name (like an enum value)

#### Program Calls

COBOL programs call other programs using `CALL`:

```cobol
PROCEDURE DIVISION.
    CALL "Calculate-Total" USING AMOUNT TAX TOTAL
    IF RETURN-CODE NOT = 0
        DISPLAY "Error in calculation"
    END-IF
```

#### Copybooks

Copybooks (`.cpy` files) are reusable code/data definitions included via `COPY`:

```cobol
DATA DIVISION.
WORKING-STORAGE SECTION.
    COPY DD-CUSTOMER-STRUCTURE.
    *> This includes a predefined customer data structure
```

### COBOL Syntax Characteristics

1. **Verbose and English-like**: Designed for business users, not just programmers
2. **Fixed Format (Traditional)**: Columns 1-6 (sequence), 7 (indicator), 8-72 (code), 73-80 (identification)
3. **Free Format (Modern)**: No column restrictions (GnuCOBOL supports both)
4. **Case Insensitive**: `MOVE`, `move`, and `Move` are equivalent
5. **Periods as Terminators**: Periods (`.`) end statements and paragraphs
6. **No Reserved Words**: COBOL has no true reserved words (context-dependent)

## COBOL Language Features

### Strengths of COBOL

#### 1. Decimal Arithmetic Precision

COBOL excels at financial calculations with exact decimal representation:

```cobol
01 PRICE                    PIC 9(5)V99 VALUE 12345.67.
01 QUANTITY                 PIC 9(3)    VALUE 100.
01 TOTAL                    PIC 9(8)V99.

COMPUTE TOTAL = PRICE * QUANTITY
*> Result: 1234567.00 (exact, no floating-point errors)
```

**Java Comparison:**
```java
// Java uses BigDecimal for precision, but it's verbose
BigDecimal price = new BigDecimal("12345.67");
BigDecimal quantity = new BigDecimal("100");
BigDecimal total = price.multiply(quantity);
```

#### 2. File Processing

COBOL has built-in, powerful file handling:

```cobol
ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT CUSTOMER-FILE ASSIGN TO "customers.dat"
        ORGANIZATION IS SEQUENTIAL
        ACCESS MODE IS SEQUENTIAL
        FILE STATUS IS FILE-STATUS.

DATA DIVISION.
FILE SECTION.
FD  CUSTOMER-FILE.
01  CUSTOMER-RECORD          PIC X(100).

PROCEDURE DIVISION.
    OPEN INPUT CUSTOMER-FILE
    PERFORM UNTIL FILE-STATUS NOT = "00"
        READ CUSTOMER-FILE
        IF FILE-STATUS = "00"
            PROCESS CUSTOMER-RECORD
        END-IF
    END-PERFORM
    CLOSE CUSTOMER-FILE
```

#### 3. Report Generation

COBOL has native report writing capabilities with automatic formatting, page breaks, and totals.

#### 4. Data Validation

Built-in validation through PICTURE clauses and editing:

```cobol
01 PHONE-NUMBER              PIC X(14) VALUE "(555) 123-4567".
*> Automatic formatting and validation
```

#### 5. String Manipulation

Powerful string operations:

```cobol
STRING FIRST-NAME DELIMITED BY SPACE
       " " DELIMITED BY SIZE
       LAST-NAME DELIMITED BY SPACE
    INTO FULL-NAME
    WITH POINTER STR-PTR
    ON OVERFLOW DISPLAY "Name too long"
END-STRING

UNSTRING ADDRESS DELIMITED BY ","
    INTO STREET CITY STATE ZIP
    TALLYING IN FIELD-COUNT
END-UNSTRING
```

#### 6. Table/Array Handling

```cobol
01 SALES-TABLE.
    05 SALES-ENTRY OCCURS 12 TIMES
       INDEXED BY SALES-INDEX.
        10 MONTH-NAME         PIC X(9).
        10 MONTH-SALES        PIC 9(8)V99.

SET SALES-INDEX TO 1
SEARCH SALES-ENTRY
    WHEN MONTH-NAME(SALES-INDEX) = "JANUARY"
        DISPLAY MONTH-SALES(SALES-INDEX)
END-SEARCH
```

#### 7. Date/Time Handling

Built-in date arithmetic:

```cobol
01 CURRENT-DATE.
    05 CD-YEAR               PIC 9(4).
    05 CD-MONTH              PIC 9(2).
    05 CD-DAY                PIC 9(2).

ACCEPT CURRENT-DATE FROM DATE
COMPUTE FUTURE-DATE = FUNCTION DATE-OF-INTEGER(
    FUNCTION INTEGER-OF-DATE(CURRENT-DATE) + 30)
```

### Modern COBOL Features (GnuCOBOL)

#### Object-Oriented Extensions

```cobol
CLASS-ID. BankAccount AS "bankaccount"
    INHERITS FROM BaseAccount.

ENVIRONMENT DIVISION.
CONFIGURATION SECTION.
REPOSITORY.
    CLASS BaseAccount IS "baseaccount".

DATA DIVISION.
WORKING-STORAGE SECTION.
METHOD-ID. deposit.
DATA DIVISION.
LINKAGE SECTION.
    01 amount                 PIC 9(10)V99.
PROCEDURE DIVISION USING amount.
    ADD amount TO balance
END METHOD deposit.
END CLASS BankAccount.
```

#### Function Support

```cobol
COMPUTE RESULT = FUNCTION SQRT(NUMBER)
COMPUTE LENGTH = FUNCTION LENGTH(STRING-VAR)
COMPUTE UPPER = FUNCTION UPPER-CASE(LOWER-STRING)
```

#### Dynamic Memory

```cobol
01 DYNAMIC-STRING            PIC X ANY LENGTH.
SET ADDRESS OF DYNAMIC-STRING TO ALLOCATE(LENGTH)
```

## Systems Built on COBOL

COBOL remains one of the most widely used programming languages in enterprise computing, particularly in critical business systems:

### Financial Services

1. **Banking Systems**
   - Core banking platforms (account management, transactions)
   - ATM networks and card processing
   - Loan processing systems
   - Interest calculation engines
   - Regulatory reporting systems

2. **Insurance**
   - Policy administration systems
   - Claims processing
   - Actuarial calculations
   - Premium billing systems

3. **Trading and Securities**
   - Stock exchange clearing systems
   - Settlement systems
   - Portfolio management
   - Risk management systems

### Government Systems

1. **Social Security Administration (USA)**
   - Benefits calculation and payment systems
   - One of the largest COBOL codebases in the world

2. **Internal Revenue Service (USA)**
   - Tax processing systems
   - Refund processing

3. **Department of Defense**
   - Payroll systems
   - Supply chain management

4. **State and Local Governments**
   - Unemployment systems
   - Welfare programs
   - Motor vehicle registration

### Healthcare

1. **Hospital Information Systems**
   - Patient billing
   - Insurance claim processing
   - Medical records (legacy systems)

2. **Pharmacy Systems**
   - Prescription processing
   - Insurance verification

### Retail and Manufacturing

1. **Point of Sale (POS) Systems**
   - Transaction processing
   - Inventory management

2. **Supply Chain Management**
   - Order processing
   - Warehouse management

### Transportation

1. **Airline Reservation Systems**
   - Flight booking (legacy systems)
   - Seat assignment

2. **Railway Systems**
   - Ticket processing
   - Scheduling systems

### Statistics

- **220 billion lines of COBOL code** in production worldwide
- **95% of ATM transactions** touch COBOL systems
- **80% of in-person transactions** at banks use COBOL
- **43% of banking systems** are built on COBOL
- **$3 trillion daily** in COBOL transactions (financial sector)

### Why COBOL Persists

1. **Proven Reliability**: Systems running for decades with minimal failures
2. **Precision**: Exact decimal arithmetic critical for financial calculations
3. **Performance**: Highly optimized for batch processing and large data volumes
4. **Investment**: Billions invested in existing systems; migration is costly and risky
5. **Regulatory Compliance**: Many systems are certified and audited; changes require re-certification
6. **Skill Availability**: Large pool of experienced COBOL developers (though aging workforce)

## COBOL Deficiencies Compared to Java

While COBOL excels in its domain, it lacks many features that modern Java developers take for granted:

### 1. Object-Oriented Programming

**COBOL:**
- Limited OOP support (only in modern extensions)
- No true classes, objects, or inheritance in traditional COBOL
- Must simulate OOP through program calls and data structures
- No encapsulation (no private/public modifiers)

**Java:**
```java
public class Account {
    private double balance;  // Encapsulation
    
    public void deposit(double amount) {
        this.balance += amount;  // 'this' reference
    }
}
```

**Impact:** COBOL requires more boilerplate code and explicit data passing.

### 2. Type System

**COBOL:**
- Weak typing (PICTURE clauses are format descriptions, not types)
- No generics or templates
- Limited type safety at compile time
- String/number conversion happens implicitly

**Java:**
```java
List<String> names = new ArrayList<>();  // Type-safe generics
int count = names.size();  // Strong typing
```

**Impact:** More runtime errors, less compiler assistance.

### 3. Exception Handling

**COBOL:**
- No exception mechanism
- Error handling via return codes
- Must check every operation manually
- No exception propagation

**Java:**
```java
try {
    processFile();
} catch (IOException e) {
    logger.error("File error", e);  // Automatic stack traces
}
```

**Impact:** Verbose error handling, easy to miss error cases.

### 4. Memory Management

**COBOL:**
- Primarily stack-based allocation
- No garbage collection
- Manual memory management for dynamic data
- Fixed-size arrays (OCCURS)

**Java:**
```java
List<Item> items = new ArrayList<>();  // Dynamic, garbage collected
items.add(new Item());  // Automatic memory management
```

**Impact:** Must manually manage data lifetimes, no dynamic collections.

### 5. Standard Library

**COBOL:**
- Limited standard library
- Must implement many common operations
- File I/O is strong, but other areas are weak
- No standard networking, JSON parsing, etc.

**Java:**
```java
import java.net.http.HttpClient;
import java.util.stream.Stream;
import com.fasterxml.jackson.databind.ObjectMapper;
// Rich ecosystem of libraries
```

**Impact:** More code to write, reinventing common functionality.

### 6. Development Tools

**COBOL:**
- Limited IDE support compared to Java
- Fewer debugging tools
- Less sophisticated refactoring
- Limited static analysis tools

**Java:**
- Excellent IDE support (IntelliJ, Eclipse, VS Code)
- Advanced debugging, profiling, refactoring
- Rich ecosystem of build tools (Maven, Gradle)

**Impact:** Slower development, harder to maintain large codebases.

### 7. Concurrency

**COBOL:**
- No built-in threading model
- Limited concurrent programming support
- Must use external mechanisms or C/C++ integration

**Java:**
```java
ExecutorService executor = Executors.newFixedThreadPool(10);
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return processData();
});
```

**Impact:** Difficult to write concurrent/multithreaded applications.

### 8. Collections and Data Structures

**COBOL:**
- Fixed-size arrays only (OCCURS)
- No dynamic lists, maps, sets
- Must implement data structures manually
- Linear search through arrays

**Java:**
```java
Map<String, Integer> counts = new HashMap<>();
counts.put("key", 42);  // O(1) lookup
List<String> items = new ArrayList<>();  // Dynamic sizing
Set<Integer> unique = new HashSet<>();  // Deduplication
```

**Impact:** Performance issues, more code for common operations.

### 9. String Handling

**COBOL:**
- Fixed-length strings (PIC X(n))
- Manual string manipulation
- No regex support (in standard COBOL)
- Verbose string operations

**Java:**
```java
String result = "Hello".substring(0, 3).toUpperCase();
if (text.matches("\\d{3}-\\d{2}-\\d{4}")) {  // Regex
    // ...
}
```

**Impact:** More verbose string operations, less powerful.

### 10. Testing Frameworks

**COBOL:**
- Limited testing frameworks
- Must build custom testing infrastructure (as CobolCraft does)
- No JUnit/JUnit 5 equivalent

**Java:**
```java
@Test
void testCalculation() {
    assertEquals(42, calculator.add(20, 22));
}
```

**Impact:** More effort to write and maintain tests.

### 11. Package Management

**COBOL:**
- No standard package manager
- Manual dependency management
- COPY statements for code reuse

**Java:**
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

**Impact:** Harder to manage dependencies and versions.

### 12. Reflection and Metaprogramming

**COBOL:**
- No reflection capabilities
- No runtime type information
- Limited metaprogramming

**Java:**
```java
Class<?> clazz = obj.getClass();
Method method = clazz.getMethod("process", String.class);
method.invoke(obj, "data");
```

**Impact:** Cannot build frameworks that rely on reflection.

### 13. Lambda Expressions and Functional Programming

**COBOL:**
- No lambda expressions
- No functional programming constructs
- Must use explicit loops

**Java:**
```java
list.stream()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

**Impact:** More verbose code for data transformations.

### 14. Annotations and Metadata

**COBOL:**
- No annotation system
- No metadata support
- Must use comments or external configuration

**Java:**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue
    private Long id;
}
```

**Impact:** Cannot use annotation-driven frameworks.

### 15. Modern Language Features

**COBOL:**
- No pattern matching
- No sealed classes
- No records (data classes)
- Limited type inference

**Java (Modern):**
```java
record Point(int x, int y) {}  // Records
sealed interface Shape permits Circle, Rectangle {}  // Sealed classes
var list = new ArrayList<String>();  // Type inference
```

### Summary: When to Use Each

**Use COBOL when:**
- Working with legacy financial/business systems
- Need exact decimal arithmetic
- Processing large batch files
- Maintaining existing COBOL codebases
- Working in regulated industries with certified systems

**Use Java when:**
- Building new applications
- Need modern language features
- Require rich ecosystem and libraries
- Building web services, APIs, microservices
- Need strong typing, OOP, and modern tooling
- Working with teams familiar with modern languages

### The CobolCraft Challenge

CobolCraft demonstrates that COBOL *can* be used for modern applications, but it requires:
- Implementing many features from scratch (JSON parsing, networking via C++)
- Working around language limitations (callbacks instead of interfaces)
- More verbose code (explicit program calls instead of methods)
- Custom tooling (code generators, testing frameworks)

This makes COBOL less suitable for new projects, but understanding these limitations helps when working with legacy systems or, as in CobolCraft's case, as an educational exercise.

## Basic Concepts

### Java → COBOL Equivalents

| Java Concept | COBOL Equivalent | Notes |
|-------------|------------------|-------|
| Class | Program (PROGRAM-ID) | COBOL programs are more like functions/procedures |
| Object Instance | Data structure in LINKAGE SECTION | Passed as parameters |
| Method | Program with LINKAGE SECTION | Programs receive parameters via LINKAGE |
| Static Method | Program with no external state | Pure function |
| Field/Property | Data item in DATA DIVISION | Can be in WORKING-STORAGE or LINKAGE |
| Package | Directory structure | COBOL uses file organization |
| Import | COPY statement | Includes copybooks (header-like files) |
| Interface | PROGRAM-POINTER | Function pointer/callback mechanism |
| Enum | Copybook with constants | Defined in `_copybooks/constants/` |
| Array | OCCURS clause | Fixed-size arrays |
| List/Collection | OCCURS with variable size | Managed manually with size counters |

## Programs vs Classes and Methods

### Java Class with Methods

```java
public class Entity {
    private UUID uuid;
    private double x, y, z;
    
    public void serialize(NbtEncoder encoder, Buffer buffer) {
        encoder.encodeUUID("UUID", uuid);
        // ...
    }
    
    public void deserialize(NbtDecoder decoder, Buffer buffer) {
        // ...
    }
}
```

### COBOL Equivalent

```cobol
*> Entity data structure (in copybook DD-ENTITY.cpy)
01 ENTITY.
    30 ENTITY-UUID              PIC X(16).
    30 ENTITY-X                 FLOAT-LONG.
    30 ENTITY-Y                 FLOAT-LONG.
    30 ENTITY-Z                 FLOAT-LONG.

*> Serialize method → separate program
IDENTIFICATION DIVISION.
PROGRAM-ID. Entity-Serialize.

DATA DIVISION.
LINKAGE SECTION.
    01 LK-ENTITY.
        COPY DD-ENTITY REPLACING LEADING ==ENTITY== BY ==LK-ENTITY==.
    01 LK-NBTENC                ...
    01 LK-BUFFER                ...

PROCEDURE DIVISION USING LK-ENTITY LK-NBTENC LK-BUFFER.
    CALL "NbtEncode-UUID" USING LK-NBTENC LK-BUFFER "UUID" LK-ENTITY-UUID
    *> ...
    GOBACK.
END PROGRAM Entity-Serialize.
```

**Key Differences:**
- Java: Methods belong to classes, operate on `this`
- COBOL: Each method is a separate program, data passed explicitly
- Java: Encapsulation via `private`/`public`
- COBOL: No access modifiers; convention-based organization

## Data Structures

### Java Class Fields

```java
public class Player {
    private UUID uuid;
    private String name;
    private Inventory inventory;
    private Location location;
}
```

### COBOL Copybook

```cobol
*> In DD-PLAYER.cpy
01 PLAYER.
    30 PLAYER-UUID              PIC X(16).
    30 PLAYER-NAME              PIC X(16).
    30 PLAYER-INVENTORY.
        COPY DD-INVENTORY REPLACING LEADING ==PREFIX== BY ==PLAYER-INVENTORY==.
    30 PLAYER-LOCATION.
        31 PLAYER-X             FLOAT-LONG.
        31 PLAYER-Y             FLOAT-LONG.
        31 PLAYER-Z             FLOAT-LONG.
```

**Key Points:**
- COBOL uses hierarchical data structures (level numbers: 01, 30, 31)
- Copybooks allow reuse via `COPY ... REPLACING`
- No automatic constructors/destructors
- Memory layout is explicit and fixed

## Inheritance and Polymorphism

### Java Inheritance

```java
public class Entity {
    public void serialize(NbtEncoder encoder, Buffer buffer) {
        // base implementation
    }
}

public class ItemEntity extends Entity {
    @Override
    public void serialize(NbtEncoder encoder, Buffer buffer) {
        super.serialize(encoder, buffer);  // call parent
        // additional serialization
    }
}
```

### COBOL Pattern (Explicit Calls)

As noted in `src/entities/base.cob`:
```cobol
*> This file contains common routines for entities. If this was Java, these routines would be found in parent
*> classes and invoked via super.method(). In COBOL, we call them explicitly.
```

**COBOL Approach:**
```cobol
*> Base entity serialization
IDENTIFICATION DIVISION.
PROGRAM-ID. EntityBase-Serialize.

PROCEDURE DIVISION USING LK-ENTITY LK-NBTENC LK-BUFFER.
    CALL "NbtEncode-UUID" USING LK-NBTENC LK-BUFFER "UUID" LK-ENTITY-UUID
    *> base fields...
    GOBACK.
END PROGRAM EntityBase-Serialize.

*> Item entity serialization
IDENTIFICATION DIVISION.
PROGRAM-ID. ItemEntity-Serialize.

PROCEDURE DIVISION USING LK-ENTITY LK-NBTENC LK-BUFFER.
    *> Call base implementation explicitly
    CALL "EntityBase-Serialize" USING LK-ENTITY LK-NBTENC LK-BUFFER
    *> Additional item-specific fields...
    GOBACK.
END PROGRAM ItemEntity-Serialize.
```

**Key Differences:**
- Java: Inheritance hierarchy, `super.method()` calls
- COBOL: Explicit composition, manual base method calls
- Java: Virtual method dispatch
- COBOL: Explicit program selection via callbacks (see below)

## Callbacks and Interfaces

### Java Interface/Strategy Pattern

```java
public interface BlockCallback {
    void onBreak(BlockState state, Player player);
}

public class TorchBlock implements BlockCallback {
    @Override
    public void onBreak(BlockState state, Player player) {
        // drop torch item
    }
}

// Registration
callbacks.register(Blocks.TORCH, new TorchBlock());
```

### COBOL Callback System

From `src/callbacks.cob` and `src/_copybooks/state/DD-CALLBACKS.cpy`:

```cobol
*> Callback storage (like a registry)
01 BLOCK-CALLBACKS EXTERNAL.
    02 CALLBACK OCCURS 2000 TIMES.
        03 CB-PTR-BLOCK-LOOT USAGE PROGRAM-POINTER.

*> Setting a callback
IDENTIFICATION DIVISION.
PROGRAM-ID. SetCallback-BlockLoot.

DATA DIVISION.
LINKAGE SECTION.
    01 LK-BLOCK-ID              BINARY-LONG.
    01 LK-CB-PTR                PROGRAM-POINTER.

PROCEDURE DIVISION USING LK-BLOCK-ID LK-CB-PTR.
    MOVE LK-CB-PTR TO CB-PTR-BLOCK-LOOT(LK-BLOCK-ID + 1)
    GOBACK.
END PROGRAM SetCallback-BlockLoot.

*> Using a callback
PROCEDURE DIVISION.
    CALL "GetCallback-BlockLoot" USING BLOCK-ID CB-PTR
    IF CB-PTR NOT = NULL
        CALL CB-PTR USING BLOCK-STATE PLAYER
    END-IF
```

**Key Points:**
- `PROGRAM-POINTER` = Java's function pointer/interface reference
- Callbacks stored in arrays indexed by block/item/entity ID
- No type safety at compile time (runtime checks needed)
- Similar to Java's `Consumer<T>`, `Function<T,R>` functional interfaces

## Memory Management

### Java (Automatic)

```java
Entity entity = new Entity();  // allocated on heap
// ... use entity ...
// Garbage collected automatically
```

### COBOL (Manual/Stack)

```cobol
DATA DIVISION.
WORKING-STORAGE SECTION.
    01 ENTITY.
        COPY DD-ENTITY REPLACING LEADING ==ENTITY== BY ====.
    *> Allocated on stack, automatically freed when program exits

LINKAGE SECTION.
    01 LK-ENTITY.
        COPY DD-ENTITY REPLACING LEADING ==ENTITY== BY ==LK-ENTITY==.
    *> Points to data passed from caller
```

**Key Differences:**
- Java: Heap allocation, garbage collection
- COBOL: Stack allocation (WORKING-STORAGE) or passed references (LINKAGE)
- Java: Objects can outlive method scope
- COBOL: Data typically scoped to program execution
- For persistent data, COBOL uses EXTERNAL storage (global/static-like)

## Error Handling

### Java Exceptions

```java
public void loadWorld(String path) throws IOException {
    File file = new File(path);
    if (!file.exists()) {
        throw new IOException("File not found: " + path);
    }
    // ...
}

try {
    loadWorld("world");
} catch (IOException e) {
    System.err.println("Error: " + e.getMessage());
}
```

### COBOL Return Codes

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. World-LoadLevel.

DATA DIVISION.
LINKAGE SECTION.
    01 LK-LEVEL-NAME            PIC X ANY LENGTH.
    01 LK-FAILURE               BINARY-CHAR UNSIGNED.
    *> 0 = success, non-zero = error

PROCEDURE DIVISION USING LK-LEVEL-NAME LK-FAILURE.
    CALL "Files-ReadAll" USING FILE-PATH BUFFER LENGTH LK-FAILURE
    IF LK-FAILURE NOT = 0
        DISPLAY "Error: Failed to read " FUNCTION TRIM(LK-LEVEL-NAME)
        GOBACK
    END-IF
    *> ...
    MOVE 0 TO LK-FAILURE
    GOBACK.
END PROGRAM World-LoadLevel.

*> Usage
CALL "World-LoadLevel" USING "world" FAILURE
IF FAILURE NOT = 0
    DISPLAY "Failed to load world"
END-IF
```

**Key Differences:**
- Java: Exception propagation, try-catch blocks
- COBOL: Return codes, explicit error checking
- Java: Unchecked exceptions can be ignored
- COBOL: Must explicitly check return codes

## Common Patterns

### 1. Registry Pattern

**Java:**
```java
public class Registry<T> {
    private Map<String, T> entries = new HashMap<>();
    
    public void register(String key, T value) {
        entries.put(key, value);
    }
    
    public T lookup(String key) {
        return entries.get(key);
    }
}
```

**COBOL:**
```cobol
*> In src/registries.cob
IDENTIFICATION DIVISION.
PROGRAM-ID. Registries-Lookup.

DATA DIVISION.
WORKING-STORAGE SECTION.
    *> Registry data stored in EXTERNAL storage
    01 REGISTRY-ENTRIES EXTERNAL.
        02 ENTRY OCCURS 10000 TIMES.
            03 ENTRY-KEY        PIC X(255).
            03 ENTRY-VALUE      BINARY-LONG.

LINKAGE SECTION.
    01 LK-REGISTRY-NAME         PIC X ANY LENGTH.
    01 LK-KEY                   PIC X ANY LENGTH.
    01 LK-VALUE                 BINARY-LONG.

PROCEDURE DIVISION USING LK-REGISTRY-NAME LK-KEY LK-VALUE.
    *> Linear search through registry
    PERFORM VARYING I FROM 1 BY 1 UNTIL I > REGISTRY-SIZE
        IF ENTRY-KEY(I) = LK-KEY
            MOVE ENTRY-VALUE(I) TO LK-VALUE
            GOBACK
        END-IF
    END-PERFORM
    *> Not found
    MOVE -1 TO LK-VALUE
    GOBACK.
END PROGRAM Registries-Lookup.
```

### 2. Builder Pattern

**Java:**
```java
Packet packet = new PacketBuilder()
    .writeVarInt(42)
    .writeString("hello")
    .build();
```

**COBOL:**
```cobol
*> Sequential calls to build packet
CALL "Encode-VarInt" USING BUFFER OFFSET 42
CALL "Encode-String" USING BUFFER OFFSET "hello"
*> Offset is updated by each call
```

### 3. Singleton Pattern

**Java:**
```java
public class Server {
    private static Server instance;
    
    public static Server getInstance() {
        if (instance == null) {
            instance = new Server();
        }
        return instance;
    }
}
```

**COBOL:**
```cobol
*> EXTERNAL storage acts like static/singleton
DATA DIVISION.
WORKING-STORAGE SECTION.
    01 SERVER-STATE EXTERNAL.
        02 SERVER-RUNNING       BINARY-CHAR UNSIGNED.
        02 SERVER-PLAYERS       ...
    *> Shared across all programs
```

## Practical Examples

### Example 1: Block Interaction

**Java (Minecraft-like):**
```java
public class DoorBlock extends Block {
    @Override
    public boolean onInteract(BlockState state, Player player, BlockFace face) {
        state = state.toggle(Properties.OPEN);
        world.setBlock(pos, state);
        return true;
    }
}
```

**COBOL (CobolCraft):**
```cobol
*> In src/blocks/door.cob
IDENTIFICATION DIVISION.
PROGRAM-ID. Door-Interact.

DATA DIVISION.
LINKAGE SECTION.
    01 LK-BLOCK-STATE           BINARY-LONG.
    01 LK-PLAYER                ...
    01 LK-FACE                  BINARY-LONG.
    01 LK-RESULT                BINARY-CHAR UNSIGNED.

PROCEDURE DIVISION USING LK-BLOCK-STATE LK-PLAYER LK-FACE LK-RESULT.
    *> Toggle open state
    CALL "BlockState-GetProperty" USING LK-BLOCK-STATE "open" PROP-VALUE
    CALL "BlockState-SetProperty" USING LK-BLOCK-STATE "open" NOT PROP-VALUE NEW-STATE
    CALL "World-SetBlock" USING BLOCK-POS NEW-STATE
    MOVE 1 TO LK-RESULT  *> success
    GOBACK.
END PROGRAM Door-Interact.

*> Registration (in initialization code)
CALL "SetCallback-BlockInteract" USING DOOR-BLOCK-STATE "Door-Interact"
```

### Example 2: Packet Handling

**Java:**
```java
public class PacketHandler {
    public void handleChatPacket(ChatPacket packet, Player player) {
        String message = packet.getMessage();
        broadcastToAll(message, player);
    }
}
```

**COBOL:**
```cobol
*> In src/packets/serverbound/play/chat.cob
IDENTIFICATION DIVISION.
PROGRAM-ID. Packet-Chat.

DATA DIVISION.
LINKAGE SECTION.
    01 LK-BUFFER                PIC X ANY LENGTH.
    01 LK-OFFSET                BINARY-LONG UNSIGNED.
    01 LK-PLAYER                ...

PROCEDURE DIVISION USING LK-BUFFER LK-OFFSET LK-PLAYER.
    *> Decode packet
    CALL "Decode-String" USING LK-BUFFER LK-OFFSET MESSAGE
    *> Broadcast
    CALL "Players-BroadcastChat" USING MESSAGE LK-PLAYER
    GOBACK.
END PROGRAM Packet-Chat.
```

### Example 3: Entity System

**Java:**
```java
public abstract class Entity {
    protected UUID uuid;
    protected double x, y, z;
    
    public abstract void tick();
    public abstract void serialize(NbtEncoder encoder);
}

public class ItemEntity extends Entity {
    private ItemStack item;
    
    @Override
    public void tick() {
        // item entity specific logic
    }
    
    @Override
    public void serialize(NbtEncoder encoder) {
        super.serialize(encoder);
        encoder.encodeCompound("Item", item);
    }
}
```

**COBOL:**
```cobol
*> Base entity (src/entities/base.cob)
IDENTIFICATION DIVISION.
PROGRAM-ID. EntityBase-Serialize.

PROCEDURE DIVISION USING LK-ENTITY LK-NBTENC LK-BUFFER.
    CALL "NbtEncode-UUID" USING LK-NBTENC LK-BUFFER "UUID" LK-ENTITY-UUID
    *> base fields...
    GOBACK.
END PROGRAM EntityBase-Serialize.

*> Item entity (src/entities/item.cob)
IDENTIFICATION DIVISION.
PROGRAM-ID. ItemEntity-Serialize.

PROCEDURE DIVISION USING LK-ENTITY LK-NBTENC LK-BUFFER.
    *> Call base
    CALL "EntityBase-Serialize" USING LK-ENTITY LK-NBTENC LK-BUFFER
    *> Item-specific
    CALL "NbtEncode-Compound" USING LK-NBTENC LK-BUFFER "Item" LK-ENTITY-ITEM-SLOT
    GOBACK.
END PROGRAM ItemEntity-Serialize.

*> Registration
CALL "SetCallback-EntitySerialize" USING ITEM-ENTITY-TYPE "ItemEntity-Serialize"
CALL "SetCallback-EntityTick" USING ITEM-ENTITY-TYPE "ItemEntity-Tick"
```

## Summary: Key Translation Rules

1. **Classes → Programs**: Each class method becomes a separate COBOL program
2. **Objects → Data Structures**: Class instances become data structures passed via LINKAGE
3. **Inheritance → Composition**: Use explicit base program calls instead of `super.method()`
4. **Interfaces → PROGRAM-POINTER**: Java interfaces map to callback function pointers
5. **Static Fields → EXTERNAL**: Shared state uses EXTERNAL storage
6. **Exceptions → Return Codes**: Error handling via return codes, not exceptions
7. **Collections → Arrays**: Use OCCURS with manual size management
8. **Generics → Copybooks with REPLACING**: Type parameters via text replacement

## Learning Path for Java Developers

1. **Start with data structures**: Understand how copybooks define "classes"
2. **Learn program calls**: See how methods become separate programs
3. **Study callback system**: Understand how polymorphism works via PROGRAM-POINTER
4. **Examine real code**: Look at `src/entities/base.cob` for inheritance patterns
5. **Review callbacks.cob**: See how the callback registry works
6. **Practice**: Try translating a simple Java class to COBOL

## Additional Resources

- GnuCOBOL Programmer's Guide: https://gnucobol.sourceforge.io/HTML/gnucobpg.html
- CobolCraft source code: Study `src/` directory for real-world examples
- Copybooks: See `src/_copybooks/` for data structure definitions

---

*This document is part of the CobolCraft project documentation. For project overview, see `proj_summary.md`.*

