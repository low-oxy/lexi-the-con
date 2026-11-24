# Interpreter
## The journey from source code to High level language, bytecode and machine code
![Compiler pipeline diagram](https://craftinginterpreters.com/image/a-map-of-the-territory/mountain.png)
*Image source: [Crafting Interpreters by Robert Nystrom](https://craftinginterpreters.com)*
- **Scanning(lexical analysis)**
the first step is scanning also known as lexing(sounds like something lex luthor would do just to get slimed by superman later) 
a scanner takes in a linear stream of characters, discards insignificant ones (like whitespace and comments), and chunks the rest into tokens.
Some characters in a source file don’t actually mean anything. Whitespace is often insignificant, and comments, by definition, are ignored by the language. The scanner usually discards these, leaving a clean sequence of meaningful tokens.

- **Parsing**
second step is to compse larger expressions and statements out of smaller parts
a parser takes the flat sequence of tokens and builds an abstract syntax tree(parse tree or syntax tree)
Parsing was first used in linguistics and NLP, and then in AI/ML domain and still is used in the AI/ML field even today.
parsing also handles the reporting of syntax errors.

  - **Static analysis**
  third step is static analysis where we analyse individual characteristics of the language
    - *Binding(Resolution)*
    we bind the identifiers usage to it's decleration
    the binding it done finding the where an indentifier is defined and where it can be used and using it's scope as an boundry for each identifier.
    - *Type check*
    after binding we will use the information from it and type check and type check the indentifiers for type compatibility and if found in compatible we will report a type error.
    - *Book keeping*
    after the semantic analysis we have to store the insights from it like using a ledger
    it can be stored by any one these methods
      - storing back as attributes itself in the syntax tree itself by using extra fields in the nodes that aren't initialised
      - storing the data in a lookup table(called a symbol table) on the side 
      - transforming the tree into a new data structure and directly espress the semantics of the code 
- **Intermediate representation**
after the initial processing we did the code might be stored in some intermediate representation before passing it to the backend.
when working or providing support for multiple source languages instead of making multiple full compilers we can create multiple front end and have one shared representation and multiple backends to get every combination.
it also has another reason for existance : Optimization
a few intermediate representation styles

  - **Three-Address Code**
  the whole code is broken down into intructions each with at most 3 addresses i.e at most 3 operands.
  this method splits complex equations into simple steps 
  this fallens a nested syntax into a linear sequence that closely resembles machine level code.
  - **Static Single-Assignment**
  this is a rule where every variable is assigned value only once.
  if a variable is updated later in the source code a new verion of the variable is created.(updating a creates a1 and so on)
  this is useful in deciding which variable to keep in the CPU
  - **Continuation-Passing Style**
  a style where instead of using a standard return statement the fucntion passes the result directly to the next function in line.
  this explicitly shows data passing between functions by invivsible control flow mechanisms(returns, loops, etc).
- **Optimization**
once the users program is processed and semantics of it is understood the code is translated to a different program with same semantics but has a more efficeint implmentation
optimization is a huge part in the realm of programming language, you probably might have seen programmers online fight about which is better high level code or low level code if you haven't well you haven't really explored "which programming lamguage is better" side of programming yet.
a few compile-time optimization techinques (think we are the compiler making the code optimised)
  - **Constant Folding**
  some codes have expressions that evaluates the exact same value, so what we do is during compile time we calculate those expresssions and replace those espressions in the code with the results of those expressions.
  - **Constant Propogation**
  some codes have static variables that are used throughout a program, so what we can do is replace those variables with the constant value to reduce memory lookups and also provide room for more constant folding.
  - **Common Subexpression Elimination**
  some codes have some calculations repeated all over the code, so what we do is calculate it once and resure the value instead of calcuated those expressions multiple times*
  - **Loop Invariant Code Motion**
  some code blocks inside a loop have expressions that never change during all the interations and move them outside the loop to execute only once.
  this is a basic explaination LSCM works differently for diff compilers( i suggest looking how LICM works in different types of interpreters).
  - **Global Value Numbering**
  here we identify redundant computaitions and assign one a unique ID(this helps in detection of redundant values) then the redundant expressions are replced by the result of those expressions.
  - **Strength Reduction**
  some codes have computationally expensive operations, we can swap them for computaionally cheaper ones
  for example instead of doing x*2 we do x+x or bitwise operations.
  bitwise operations are really intresting while normal operations are sending letters to the cpu through a pigeon bitwise is like being on a date.
  - **Loop Unrolling**
  we expand the whole loop by repeating the body inside loop to number of times it's used for example if a loop will run for 4 times repeat the code 4 times.
  and this helps because we the CPU at runtime won't have to check the condition every time the loop body runs.

- **Code generation**
Now that the user code is processed and optimized now we convert it into something machine or cpu can understand i.e assembly-like intructions.
but we have a to pick one of two paths write code for a real CPU or for a virtual one, both have thier own use case
if you want lighting fast code and direct native code writing code for CPU is an option but this means your compiler would be locked to a single cpu architecture or you would have to write them for every CPU you want them to run on.
else if you want them to run on every CPU we write code for the viritual machine(a hypothetical, idealized machine) that can run on any cpu architecture.
it is called byte code since each intruction is often a byte long(also called p-code by Niklaus Wirth where p stands for portable).
after we produce a byte code we still have to translate it to something a chip can understand, we can solve this either by making a mini compiler for each CPU architecture (the whole complete architecture will be like a Matryoshka doll in a way)or we can write code for a VM.
VM's is another rabbit hole and a whole field on it's own(might cover on this later) but basically we tranlate the byte code to be ran on a vm which emulates a hypetheical cpu and now the whole compiler will run anywhere the vm can run.

-  **Runtime**
Now that we have prepared dinner following all the procedures, time to eat it.
we tell the OS to load the executable(complied machine code) and the OS will run it.(if a byte code then we need to start up the VM and load the code into it)
now if you explored the optimization rabbit hole i talked about earlier, you would have stumbled upon "services" that high level languages provide which is done manually in low level code. now depending on the interpreter we are building we may or may not need to implement these services.
they are intresting and each language provides diff sets of services one of them for example is JIT.
JIT: just in time compilation is a service that converts only the most frequently run parts of the code(hot spots) and compiles them directly into the machine code while program is still executing.(there's many services a language can provide look into them and pick which one you wanna provide in your language/compiler)

have you ever installed an application and got the message "Please install Java to run this application." and might have thought why do i need to install java to run this, well that's because java programs need interpreter to run well what is that? well a code can run in two ways
  - The Backpack(Embedded runtime)
  In a fully compiled language, the compiler takes your source code, translates it to machine code, and then takes the entire runtime system (garbage collector, thread scheduler, etc.) and glues it directly into your final executable file.
  the .exe created by this is completely self sufficent.
  - The Host Machine(VM / Interpreter)
  The compiler does not put the runtime into your program's file. Your output file just contains lightweight instructions (bytecode or plain text). The runtime exists as an entirely separate software application installed on the user's computer, called a Virtual Machine (VM) or Interpreter.
  to run compiled code created by this you will require it's own vm or interpreter(hence the "Please install Java to run this application.").

- **Compilers and Interpreters**
**Compilier** uses an implementation technique that involves translating source language into some other usually low level form, but doesn't execute it, the user has to do it themselves.
**Interpreters** whereas here we take in the source code and execute it immediately without needing the user to run it seperately.
There are pure compilers(Go,Rust), Pure interpreters(Bash/Shell scripts) and Hybrid(compiles to bytecode and then interprets in VM)

- **Skipping traffic**
What we just covered is the full, long route with every possible phase. Not all languages go through this journey they skip or take some alternative routes.
  - **Single-pass compilers**
  In single pass compilers the compiler will interleave parsing, analysis, and code generation so that they produce output code directly in the parser, without allocating any syntax trees or other IRs.You have no intermediate data structures to store global information about the program, and you don’t revisit any previously parsed part of the code. That means as soon as you see some expression, you need to know enough to correctly compile it.
  This is made possible by using Syntax-Directed Translation (SDT), where we define a code-generation action for a specific grammar rule so that when the parser recognises that piece of syntax, it immediately fires the corresponding action.
  This method of compiling was/is useful if memory is scarce.
  - **Tree-walk interpreter**
  In this we execute the code right after parsing it to an AST.
  each node is evaluated by traversing the AST one branch and leaf at a time
  - **Transpilers**
  Instead of doing all the work we can just write the front end for our soruce language and compile it into another high level IR or target language that already has a back end built for it. That backend will then take care the rest of the work. And more the semantically similar the source lanugage and target IR are less optimization and analysis we have to do.
  It's also used heavily for compatibilty especially in browsers tsc, babel and any other front end language you used to need it to be compiled into javascript for it to be able to run in any browser(webassembly made it possible for Rust, C++, and Go to run in the browser without compiling to JavaScript)
## References
* Primary reference [**Crafting Interpreters**](https://craftinginterpreters.com)
  