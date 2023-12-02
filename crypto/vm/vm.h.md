[Link](https://chat.openai.com/c/5f74f985-b8cb-4dd7-958e-14f6fdef5356)

This code appears to be a C++ implementation for a virtual machine (VM) used in the TON Blockchain Library. Let's break down some key components:

1. **Header Comments**: The code begins with comments providing information about the software's licensing terms, including the use of the GNU Lesser General Public License. It also mentions that the code is part of the TON Blockchain Library and provides copyright information.

2. **Header Guard**: The `#pragma once` directive is used for header guards. It ensures that the header file is only included once in a translation unit.

3. **Includes**: The code includes various C++ headers, such as `refcnt.hpp`, `cellslice.h`, `stack.hpp`, `vmstate.h`, `log.h`, `continuation.h`, and others. These headers likely define necessary classes and functionalities used in the code.

4. **Namespace**: The code uses the `vm` namespace to encapsulate its declarations.

5. **Type Aliases and Structures**: Several type aliases (`using`) and structures are declared, including `GasLimits`, `CommittedState`, and others. These structures likely hold important state information for the VM.

6. **GasLimits**: Defines a structure to manage gas limits and consumption during VM execution.

7. **CommittedState**: Represents a state with committed changes, including references to specific cells.

8. **VmState**: The main class representing the VM state. It has member functions for managing gas, executing instructions, handling exceptions, and more.

9. **Constants**: Several constants related to gas prices and gas consumption for various operations are defined as static members of the `VmState` class.

10. **Gas Consumption Functions**: Functions like `consume_gas_chk`, `consume_gas`, and others manage gas consumption with checks.

11. **Library Handling**: The code includes functions to register and load libraries. Libraries seem to be collections of cells.

12. **Code Execution Functions**: Functions like `step`, `run`, `call`, `jump`, `ret`, and others handle the execution flow of the VM.

13. **Error Handling**: The code includes functions for handling exceptions and fatal errors.

14. **Versioning**: There is some versioning logic (`global_version`) that influences certain behaviors.

15. **Miscellaneous Functions**: Other functions include ones for adjusting control registers, obtaining state hashes, and more.

16. **ParentVmState**: A structure representing a parent VM state, which is used when running a child VM. It includes information about what data to return, whether to return actions, gas isolation, and the number of return values.

17. **Run VM Code Functions**: Functions for running VM code, either by passing a stack or directly modifying a provided stack.

18. **Library Lookup Function**: `lookup_library_in` appears to be a function for looking up a library based on a key.

19. **Initialization Function**: The `init_vm` function initializes the VM.

This code is complex and likely part of a larger blockchain library. Understanding its complete functionality would require a thorough analysis of the entire codebase.

***



Sure, let's list the main entities and provide short descriptions based on the provided C++ code:

1. **GasLimits**:
   - **Description**: Represents gas limits and consumption during VM execution.
   - **Usage**: Manages gas-related parameters such as gas_max, gas_limit, gas_credit, and gas_remaining.

2. **CommittedState**:
   - **Description**: Represents a state with committed changes, including references to specific cells.
   - **Usage**: Used to store committed changes in the VM state.

3. **VmState**:
   - **Description**: The main class representing the VM state.
   - **Usage**: Manages the execution of the virtual machine, including gas consumption, code execution, stack management, and control registers.

4. **ControlRegs**:
   - **Description**: Represents control registers used in the VM state.
   - **Usage**: Manages control registers such as continuations, data cells, and tuple registers.

5. **ParentVmState**:
   - **Description**: Represents a parent VM state when running a child VM.
   - **Usage**: Holds information about whether to return data, return actions, isolate gas, and the number of return values.

6. **DispatchTable**:
   - **Description**: Represents a dispatch table for VM instructions.
   - **Usage**: Used to dispatch VM instructions based on opcode.

7. **Stack**:
   - **Description**: Represents the VM stack.
   - **Usage**: Manages the stack of the VM, including operations like push, pop, and checking stack underflow.

8. **VmLog**:
   - **Description**: Represents the log of VM operations.
   - **Usage**: Used to store logs related to VM execution.

9. **QuitCont**:
   - **Description**: Represents a continuation for quitting VM execution.
   - **Usage**: Used in the context of VM execution.

10. **Tuple**:
    - **Description**: Represents a tuple in the VM.
    - **Usage**: Used for managing and manipulating tuples in the VM state.

11. **OrdCont**:
    - **Description**: Represents an ordinary continuation in the VM.
    - **Usage**: Used for managing ordinary continuations during VM execution.

12. **Ref**:
    - **Description**: Represents a reference to an object.
    - **Usage**: Used for managing references to various types of objects, including cells and continuations.

These entities collectively form the foundation for the TON Blockchain Library's virtual machine implementation, providing functionalities for gas management, state representation, code execution, and more.


***



***

Certainly! Let's take a deeper dive into the key classes and structures in the provided C++ code from the TON Blockchain Library:

### 1. **GasLimits Class:**
```cpp
struct GasLimits {
  // ...
};
```
- **Description:** Manages gas limits and consumption during VM execution.
- **Members:**
  - `gas_max`: Maximum allowed gas.
  - `gas_limit`: Gas limit for the current operation.
  - `gas_credit`: Gas credit, an additional allowance.
  - `gas_remaining`: Remaining gas after consumption.
  - `gas_base`: Initial gas value.
- **Methods:**
  - `gas_consumed()`: Calculates the gas consumed so far.
  - `set_limits()`: Sets new gas limits.
  - `change_base()`: Changes the base gas value.
  - `change_limit()`: Changes the gas limit.
  - `consume()`: Consumes a specified amount of gas.
  - `try_consume()`: Tries to consume gas and returns success.
  - `gas_exception()`: Throws a gas exception if conditions are not met.
  - `consume_chk()`: Consumes gas and throws an exception if unsuccessful.
  - `check()`: Checks if gas remaining is non-negative.
  - `final_ok()`: Checks if the final gas state is valid.

### 2. **CommittedState Class:**
```cpp
struct CommittedState {
  Ref<vm::Cell> c4, c5;
  bool committed{false};
};
```
- **Description:** Represents a state with committed changes, including references to specific cells.
- **Members:**
  - `c4`, `c5`: References to committed cells.
  - `committed`: Flag indicating whether changes are committed.

### 3. **VmState Class:**
```cpp
class VmState final : public VmStateInterface {
  // ...
};
```
- **Description:** The main class representing the VM state.
- **Members:**
  - `code`: Reference to the code being executed.
  - `stack`: Stack used for VM operations.
  - `cr`: Control registers holding continuations and data.
  - `cstate`: Committed state.
  - `cp`: Instruction pointer.
  - `steps`: Number of executed steps.
  - `dispatch`: Dispatch table for VM instructions.
  - `quit0`, `quit1`: Quit continuations.
  - `log`: Log of VM operations.
  - `gas`: Gas limits and consumption.
  - `libraries`: Vector of library cells.
  - `loaded_cells`: Set of loaded cell hashes.
  - `stack_trace`: Stack trace depth.
  - `debug_off`: Debug mode flag.
  - `chksig_always_succeed`: Flag indicating if chksig always succeeds.
  - `missing_library`: Optional missing library hash.
  - `max_data_depth`: Maximum allowed data depth.
  - `global_version`: Global version for certain behaviors.
  - `chksgn_counter`: Counter for chksgn calls.
  - `parent`: Pointer to the parent VM state.
- **Methods:**
  - Various functions for stack manipulation, gas management, control register handling, code execution, and error handling.
  - `set_gas_limits()`: Sets gas limits.
  - `final_gas_ok()`: Checks if final gas state is valid.
  - `committed()`: Checks if changes are committed.
  - `get_committed_state()`: Gets the committed state.
  - `consume_gas_chk()`: Consumes gas with checks.
  - `consume_gas()`: Consumes gas.
  - `consume_tuple_gas()`: Consumes gas based on tuple length.
  - `consume_stack_gas()`: Consumes gas based on stack depth.
  - `get_gas_limits()`: Gets gas limits.
  - `change_gas_limit()`: Changes the gas limit.
  - `check_underflow()`: Checks for stack underflow.
  - `register_library_collection()`: Registers a library collection.
  - `load_library()`: Loads a library based on a hash.
  - `register_cell_load()`: Registers a loaded cell.
  - `register_cell_create()`: Registers a created cell.
  - `init_cp()`, `set_cp()`, `force_cp()`: Functions for managing the instruction pointer.
  - `get_cp()`: Gets the current instruction pointer.
  - `incr_stack_trace()`: Increments the stack trace depth.
  - `get_steps_count()`: Gets the number of executed steps.
  - `get_state_hash()`: Gets the state hash.
  - `get_final_state_hash()`: Gets the final state hash.
  - `step()`: Executes a single step.
  - `run()`: Runs the VM.
  - `get_stack()`, `get_stack_const()`, `get_stack_ref()`: Gets references to the stack.
  - Various functions for setting and getting control registers, stack entries, and log information.
  - `define_c0()`, `set_c0()`, `set_c1()`, `set_c2()`, `set_c()`, `set_d()`, `set_c4()`, `set_c7()`: Functions for setting control registers.
  - `consume_gas()`: Consumes gas.
  - `set_code()`: Sets the code and instruction pointer.
  - `push_code()`: Pushes the code onto the stack.
  - `adjust_cr()`, `preclear_cr()`: Adjusts or preclears control registers.
  - `get_global_version()`, `set_global_version()`: Gets or sets the global version.
  - `call()`, `jump()`, `ret()`, `ret_alt()`, `repeat()`, `again()`, `until()`, `loop_while()`: Control flow functions.
  - `throw_exception()`: Throws an exception.
  - `extract_cc()`: Extracts a continuation.
  - `c1_envelope()`: Envelopes a continuation.
  - `c1_save_set()`: Saves control register C1.
  - `fatal()`: Throws a fatal error.
  - `jump_to()`: Jumps to a continuation.
  - `convert_code_cell()`: Converts a code cell to a code slice.
  - `try_commit()`, `force_commit()`: Tries or forces commitment of changes.
  - `set_chksig_always_succeed()`, `get_chksig_always_succeed()`: Sets or gets the chksig_always_succeed flag.
  - `get_missing_library()`: Gets the missing library hash.
  - `set_max_data_depth()`: Sets the maximum data depth.
  - `run_child_vm()`, `restore_parent_vm()`: Runs a child VM or restores a parent VM.

### 4. **ParentVmState Structure:**
```cpp
struct ParentVmState {
  VmState state;
  bool return_data, return_actions, return_gas, isolate_gas;
  int ret_vals;
};
```
- **Description:** Represents a parent VM state when running a child VM.
- **Members:**
  - `state`: VmState object representing the parent VM state.
  - `return_data`: Flag indicating whether to return data.
  - `return_actions`: Flag indicating whether to return actions.
  - `return_gas`: Flag indicating
 


***


Certainly, let's dive into the details for some key methods and functions in the provided C++ code:

### 1. **GasLimits Class:**
#### `GasLimits()`
- **Description:** Constructor for GasLimits class.
- **Usage:** Initializes gas limits with default values.

#### `gas_consumed() const`
- **Description:** Calculates and returns the gas consumed.
- **Usage:** Used to determine the amount of gas consumed during execution.

#### `set_limits(long long _max, long long _limit, long long _credit = 0)`
- **Description:** Sets new gas limits.
- **Parameters:**
  - `_max`: New maximum gas limit.
  - `_limit`: New gas limit for the current operation.
  - `_credit`: New gas credit (default is 0).
- **Usage:** Adjusts the gas limits based on the provided parameters.

#### `change_base(long long _base)`
- **Description:** Changes the base gas value.
- **Parameters:**
  - `_base`: New base gas value.
- **Usage:** Adjusts the base gas value during execution.

#### `change_limit(long long _limit)`
- **Description:** Changes the gas limit.
- **Parameters:**
  - `_limit`: New gas limit.
- **Usage:** Adjusts the gas limit during execution.

#### `consume(long long amount)`
- **Description:** Consumes a specified amount of gas.
- **Parameters:**
  - `amount`: Amount of gas to consume.
- **Usage:** Decreases the remaining gas by the specified amount.

#### `try_consume(long long amount) const`
- **Description:** Tries to consume gas and returns success.
- **Parameters:**
  - `amount`: Amount of gas to try to consume.
- **Usage:** Checks if there is enough remaining gas to consume the specified amount.

#### `gas_exception() const`
- **Description:** Throws a gas exception if conditions are not met.
- **Usage:** Checks if gas limits have been violated and throws an exception if they have.

#### `gas_exception(bool cond) const`
- **Description:** Throws a gas exception if a condition is not met.
- **Parameters:**
  - `cond`: Condition to check.
- **Usage:** Checks the condition and throws a gas exception if it is not met.

#### `consume_chk(long long amount)`
- **Description:** Consumes gas and throws an exception if unsuccessful.
- **Parameters:**
  - `amount`: Amount of gas to consume.
- **Usage:** Checks if there is enough remaining gas to consume the specified amount and throws an exception if not.

#### `check() const`
- **Description:** Checks if gas remaining is non-negative.
- **Usage:** Throws a gas exception if the remaining gas is negative.

#### `final_ok() const`
- **Description:** Checks if the final gas state is valid.
- **Usage:** Determines if the final gas state is valid after execution.

### 2. **CommittedState Class:**
#### `CommittedState()`
- **Description:** Default constructor for CommittedState class.
- **Usage:** Initializes CommittedState with default values.

### 3. **VmState Class:**
#### `VmState()`
- **Description:** Default constructor for VmState class.
- **Usage:** Initializes a VmState object with default values.

#### `VmState(Ref<CellSlice> _code)`
- **Description:** Constructor for VmState class with code.
- **Parameters:**
  - `_code`: Reference to the code being executed.
- **Usage:** Initializes a VmState object with provided code and default values.

#### `VmState(Ref<CellSlice> _code, Ref<Stack> _stack, int flags = 0, Ref<Cell> _data = {}, VmLog log = {}, std::vector<Ref<Cell>> _libraries = {}, Ref<Tuple> init_c7 = {})`
- **Description:** Constructor for VmState class with various parameters.
- **Parameters:**
  - `_code`: Reference to the code being executed.
  - `_stack`: Reference to the stack.
  - `flags`: Flags for VM state initialization (default is 0).
  - `_data`: Reference to additional data (default is empty cell).
  - `log`: VmLog object for logging (default is empty log).
  - `_libraries`: Vector of library cells (default is empty vector).
  - `init_c7`: Reference to Tuple for initialization (default is empty tuple).
- **Usage:** Initializes a VmState object with the provided parameters.

#### `VmState(Ref<Cell> code_cell, Args&&... args)`
- **Description:** Constructor for VmState class using a code cell and additional parameters.
- **Parameters:**
  - `code_cell`: Reference to the code cell.
  - `args`: Additional arguments for state initialization.
- **Usage:** Initializes a VmState object with a code cell and additional parameters.

#### `bool set_gas_limits(long long _max, long long _limit, long long _credit = 0)`
- **Description:** Sets gas limits.
- **Parameters:**
  - `_max`: New maximum gas limit.
  - `_limit`: New gas limit for the current operation.
  - `_credit`: New gas credit (default is 0).
- **Usage:** Sets gas limits based on provided parameters.

#### `bool final_gas_ok() const`
- **Description:** Checks if the final gas state is valid.
- **Usage:** Determines if the final gas state is valid after execution.

#### `long long gas_consumed() const`
- **Description:** Calculates and returns the gas consumed.
- **Usage:** Used to determine the amount of gas consumed during execution.

#### `bool committed() const`
- **Description:** Checks if changes are committed.
- **Usage:** Returns true if changes are committed, false otherwise.

#### `const CommittedState& get_committed_state() const`
- **Description:** Gets the committed state.
- **Usage:** Returns a const reference to the committed state.

#### `void consume_gas_chk(long long amount)`
- **Description:** Consumes gas with checks.
- **Parameters:**
  - `amount`: Amount of gas to consume.
- **Usage:** Consumes gas and checks if the operation is successful.

#### `void consume_gas(long long amount)`
- **Description:** Consumes gas.
- **Parameters:**
  - `amount`: Amount of gas to consume.
- **Usage:** Consumes gas without additional checks.

#### `void consume_tuple_gas(unsigned tuple_len)`
- **Description:** Consumes gas based on tuple length.
- **Parameters:**
  - `tuple_len`: Length of the tuple.
- **Usage:** Consumes gas based on the length of a tuple.

#### `void consume_stack_gas(unsigned stack_depth)`
- **Description:** Consumes gas based on stack depth.
- **Parameters:**
  - `stack_depth`: Depth of the stack.
- **Usage:** Consumes gas based on the depth of the stack.

#### `GasLimits get_gas_limits() const`
- **Description:** Gets gas limits.
- **Usage:** Returns the current gas limits.

#### `void change_gas_limit(long long new_limit)`
- **Description:** Changes the gas limit.
- **Parameters:**
  - `new_limit`: New gas limit.
- **Usage:** Changes the gas limit during execution.

#### `template <typename... Args> void check_underflow(

Args... args)`
- **Description:** Checks for stack underflow.
- **Parameters:**
  - `args`: Arguments for checking underflow.
- **Usage:** Checks if there is sufficient data on the stack for the given arguments.

#### `bool register_library_collection(Ref<Cell> lib)`
- **Description:** Registers a library collection.
- **Parameters:**
  - `lib`: Reference to the library collection.
- **Usage:** Registers a library collection for use in the VM.

#### `Ref<Cell> load_library(td::ConstBitPtr hash)`
- **Description:** Loads a library based on a hash.
- **Parameters:**
  - `hash`: Pointer to the hash.
- **Usage:** Loads a library cell based on the provided hash.

#### `void register_cell_load(const CellHash& cell_hash)`
- **Description:** Registers a loaded cell.
- **Parameters:**
  - `cell_hash`: Cell hash to register.
- **Usage:** Registers a cell that has been loaded during execution.

#### `void register_cell_create()`
- **Description:** Registers a created cell.
- **Usage:** Registers a cell that has been created during execution.

#### `bool init_cp(int new_cp)`
- **Description:** Initializes the instruction pointer.
- **Parameters:**
  - `new_cp`: New instruction pointer value.
- **Usage:** Initializes the instruction pointer based on the provided value.

#### `bool set_cp(int new_cp)`
- **Description:** Sets the instruction pointer.
- **Parameters:**
  - `new_cp`: New instruction pointer value.
- **Usage:** Sets the instruction pointer based on the provided value.

#### `void force_cp(int new_cp)`
- **Description:** Forces the instruction pointer to a new value.
- **Parameters:**
  - `new_cp`: New instruction pointer value.
- **Usage:** Forces the instruction pointer to the provided value.

#### `int get_cp() const`
- **Description:** Gets the current instruction pointer.
- **Usage:** Returns the current value of the instruction pointer.

#### `int incr_stack_trace(int v)`
- **Description:** Increments the stack trace depth.
- **Parameters:**
  - `v`: Value to increment the stack trace depth by.
- **Usage:** Increments the stack trace depth by the provided value.

#### `long long get_steps_count() const`
- **Description:** Gets the number of executed steps.
- **Usage:** Returns the number of steps executed so far.

#### `td::BitArray<256> get_state_hash() const`
- **Description:** Gets the state hash.
- **Usage:** Returns the hash of the current state.

#### `td::BitArray<256> get_final_state_hash(int exit_code) const`
- **Description:** Gets the final state hash.
- **Parameters:**
  - `exit_code`: Exit code to include in the hash.
- **Usage:** Returns the hash of the final state.

#### `int step()`
- **Description:** Executes a single step.
- **Usage:** Executes a single step of the VM.

#### `int run()`
- **Description:** Runs the VM.
- **Usage:** Executes the VM until completion.

#### `Stack& get_stack()`
- **Description:** Gets a reference to the stack.
- **Usage:** Returns a reference to the stack for manipulation.

#### `const Stack& get_stack_const() const`
- **Description:** Gets a constant reference to the stack.
- **Usage:** Returns a constant reference to the stack for reading.

#### `Ref<Stack> get_stack_ref() const`
- **Description:** Gets a reference to the stack.
- **Usage:** Returns a shared reference to the stack.

#### `Ref<Continuation> get_c0() const`
- **Description:** Gets the value of control register C0.
- **Usage:** Returns a reference to the value of control register C0.

#### `Ref<Continuation> get_c1() const`
- **Description:** Gets the value of control register C1.
- **Usage:** Returns a reference to the value of control register C1.

#### `Ref<Continuation> get_c2() const`
- **Description:** Gets the value of control register C2.
- **Usage:** Returns a reference to the value of control register C2.

#### `Ref<Continuation> get_c3() const`
- **Description:** Gets the value of control register C3.
- **Usage:** Returns a reference to the value of control register C3.

#### `Ref<Cell> get_c4() const`
- **Description:** Gets the value of control register C4.
- **Usage:** Returns a reference to the value of control register C4.

#### `Ref<Tuple> get_c7() const`
- **Description:** Gets the value of control register C7.
- **Usage:** Returns a reference to the value of control register C7.

#### `Ref<Continuation> get_c(unsigned idx) const`
- **Description:** Gets the value of a specific control register.
- **Parameters:**
  - `idx`: Index of the control register.
- **Usage:** Returns a reference to the value of the specified control register.

#### `Ref<Cell> get_d(unsigned idx) const`
- **Description:** Gets the value of a specific data register.
- **Parameters:**
  - `idx`: Index of the data register.
- **Usage:** Returns a reference to the value of the specified data register.

#### `StackEntry get(unsigned idx) const`
- **Description:** Gets a stack entry at a specific index.
- **Parameters:**
  - `idx`: Index of the stack entry.
- **Usage:** Returns a stack entry at the specified index.

#### `const VmLog& get_log() const`
- **Description:** Gets the log of VM operations.
- **Usage:** Returns a constant reference to the log of VM operations.

#### `void define_c0(Ref<Continuation> cont)`
- **Description:** Defines

***

Given the size and complexity of the code, providing detailed explanations for every method and function is not feasible within this context. However, I can certainly provide explanations for a few representative and critical methods to give you an idea of the overall functionality. If there are specific methods you're interested in, please let me know, and I can provide more detailed explanations for those.

### 1. `GasLimits` Class Methods:

#### `gas_consumed()`
- **Description:** Calculates the gas consumed so far.
- **Implementation:** Returns the difference between the initial gas (`gas_base`) and the remaining gas (`gas_remaining`).

#### `set_limits(long long _max, long long _limit, long long _credit = 0)`
- **Description:** Sets new gas limits.
- **Implementation:** Updates the maximum gas, gas limit, gas credit, and recalculates the remaining gas based on the new limits.

#### `change_base(long long _base)`
- **Description:** Changes the base gas value.
- **Implementation:** Adjusts the remaining gas based on the new base value.

#### `change_limit(long long _limit)`
- **Description:** Changes the gas limit.
- **Implementation:** Updates the gas limit and adjusts the remaining gas if needed.

#### `consume(long long amount)`
- **Description:** Consumes a specified amount of gas.
- **Implementation:** Decreases the remaining gas by the specified amount.

#### `try_consume(long long amount)`
- **Description:** Tries to consume gas and returns success.
- **Implementation:** Attempts to consume gas and returns true if successful, false otherwise.

#### `gas_exception()`
- **Description:** Throws a gas exception if conditions are not met.
- **Implementation:** Throws an exception if the remaining gas is negative.

#### `consume_chk(long long amount)`
- **Description:** Consumes gas and throws an exception if unsuccessful.
- **Implementation:** Calls `try_consume` and throws a gas exception if it returns false.

#### `check()`
- **Description:** Checks if gas remaining is non-negative.
- **Implementation:** Calls `gas_exception` to throw an exception if the remaining gas is negative.

#### `final_ok()`
- **Description:** Checks if the final gas state is valid.
- **Implementation:** Returns true if the remaining gas is greater than or equal to the gas credit.

### 2. `CommittedState` Class:

#### Constructor:
- **Description:** Initializes the `CommittedState` object.
- **Implementation:** Takes references to committed cells (`c4` and `c5`) and sets the `committed` flag.

### 3. `VmState` Class Methods:

#### `VmState(Ref<Cell> code_cell, Args&&... args)`
- **Description:** Constructor that takes a code cell and additional arguments.
- **Implementation:** Converts the code cell to a code slice and calls the main constructor with the code slice and other arguments.

#### `init_vm(bool enable_debug = false)`
- **Description:** Initializes the VM.
- **Implementation:** Performs initialization tasks, potentially enabling debug mode.

#### `set_gas_limits(long long _max, long long _limit, long long _credit = 0)`
- **Description:** Sets gas limits for the VM.
- **Implementation:** Updates gas limits in the `gas` member.

#### `final_gas_ok()`
- **Description:** Checks if the final gas state is valid.
- **Implementation:** Calls `gas.final_ok()` to check if remaining gas is greater than or equal to gas credit.

#### `committed() const`
- **Description:** Checks if changes are committed.
- **Implementation:** Returns the value of `cstate.committed`.

#### `get_committed_state() const`
- **Description:** Gets the committed state.
- **Implementation:** Returns the `CommittedState` object.

#### `consume_gas_chk(long long amount)`
- **Description:** Consumes gas with checks.
- **Implementation:** Calls `gas.consume_chk(amount)`.

#### `consume_gas(long long amount)`
- **Description:** Consumes gas.
- **Implementation:** Calls `gas.consume(amount)`.

#### `consume_tuple_gas(unsigned tuple_len)`
- **Description:** Consumes gas based on tuple length.
- **Implementation:** Calls `consume_gas` with the appropriate amount based on the tuple length.

#### `consume_stack_gas(unsigned stack_depth)`
- **Description:** Consumes gas based on stack depth.
- **Implementation:** Calls `consume_gas` with the appropriate amount based on the stack depth.

#### `get_gas_limits() const`
- **Description:** Gets gas limits.
- **Implementation:** Returns the `gas` member.

#### `change_gas_limit(long long new_limit)`
- **Description:** Changes the gas limit.
- **Implementation:** Calls `gas.change_limit(new_limit)`.

#### `check_underflow(Args... args)`
- **Description:** Checks for stack underflow.
- **Implementation:** Calls `stack->check_underflow(args...)`.

#### `register_library_collection(Ref<Cell> lib)`
- **Description:** Registers a library collection.
- **Implementation:** Adds the library cell to the `libraries` vector.

#### `load_library(td::ConstBitPtr hash)`
- **Description:** Loads a library based on a hash.
- **Implementation:** Calls `lookup_library_in` with the provided hash.

#### `register_cell_load(const CellHash& cell_hash)`
- **Description:** Registers a loaded cell.
- **Implementation:** Adds the cell hash to the `loaded_cells` set.

#### `register_cell_create()`
- **Description:** Registers a created cell.
- **Implementation:** Calls `consume_gas` with the appropriate gas price.

#### `init_cp(int new_cp)`
- **Description:** Initializes the instruction pointer.
- **Implementation:** Sets the instruction pointer (`cp`) to the specified value.

#### `set_cp(int new_cp)`
- **Description:** Sets the instruction pointer.
- **Implementation:** Sets the instruction pointer (`cp`) to the specified value.

#### `force_cp(int new_cp)`
- **Description:** Forces the instruction pointer to a new value.
- **Implementation:** Calls `set_cp` and consumes gas based on the gas price for instruction pointer manipulation.

#### `get_cp() const`
- **Description:** Gets the current instruction pointer.
- **Implementation:** Returns the value of `cp`.

#### `incr_stack_trace(int v)`
- **Description:** Increments the stack trace depth.
- **Implementation:** Increments the `stack_trace` member by the specified value and returns the result.

#### `get_steps_count() const`
- **Description:** Gets the number of executed steps.
- **Implementation:** Returns the value of `steps`.

#### `get_state_hash() const`
- **Description:** Gets the state hash.
- **Implementation:** Calls `get_stack_const()->get_state_hash()`.

#### `get_final_state_hash(int exit_code) const`
- **Description:** Gets the final state hash.
- **Implementation:** Calls `get_stack_const()->get_final_state_hash(exit_code)`.

#### `step()`
- **Description:** Executes a single step.
- **Implementation:** Calls `run_inner` and increments the step count.

#### `run()`
- **Description:** Runs the VM until completion.
- **Implementation:** Calls `run_inner` repeatedly until the VM completes or encounters an exception.

This is a selection of methods from the `VmState` class. If you're interested in more specific methods or have other questions, feel free to ask.

***

Certainly, let's provide a concise insight into the provided C++ code:

### 1. **Gas Management (`GasLimits` Class):**
   - Manages gas limits and consumption during VM execution.
   - Tracks maximum gas, gas limit, gas credit, and remaining gas.
   - Provides methods to consume gas, set limits, and check gas conditions.

### 2. **VM State (`VmState` Class):**
   - Represents the state of the virtual machine.
   - Manages code execution, stack operations, gas consumption, and control flow.
   - Includes gas limits, control registers, stack, and committed state.
   - Supports various VM operations, including calls, jumps, returns, and exception handling.
   - Handles library management, gas consumption, and code execution.

### 3. **Control Registers (`ControlRegs` Struct):**
   - Holds continuations (`c0` to `c3`), data cells (`d0` to `d3`), and special registers.
   - Allows manipulation of control registers for managing VM state.

### 4. **Gas Pricing Constants:**
   - Defines gas pricing constants for various VM operations (e.g., cell loading, hashing, arithmetic).
   - Gas prices influence the cost of executing different VM instructions.

### 5. **Committed State (`CommittedState` Struct):**
   - Represents a state with committed changes, including references to specific cells.
   - Tracks committed cells (`c4` and `c5`) and a flag indicating whether changes are committed.

### 6. **Library Management:**
   - Supports the registration and loading of external library cells.
   - Tracks loaded cells and manages gas consumption during library operations.

### 7. **Execution and Control Flow:**
   - Implements methods for various control flow operations (e.g., calls, jumps, returns, loops).
   - Handles exceptions, stack manipulation, and code execution.

### 8. **Logging (`VmLog` Class):**
   - Manages logging of VM operations for debugging and analysis.

### 9. **Parent VM State (`ParentVmState` Struct):**
   - Represents a parent VM state when running a child VM.
   - Includes the parent VM state (`VmState`), flags for return types, and the number of return values.

### 10. **Dispatch Table (`DispatchTable`):**
   - Not explicitly shown but likely present, it would contain a table of VM instruction handlers.

### 11. **Cryptographic Operations:**
   - Includes gas pricing and methods for cryptographic operations (e.g., signature verification).

### 12. **Error Handling:**
   - Provides mechanisms for throwing and handling VM exceptions.

### 13. **Code Execution Loop (`run_vm_code` Function):**
   - Executes the VM code in a loop, managing stack, gas, and control flow.

### 14. **Initialization (`init_vm` Function):**
   - Initializes the VM, potentially enabling debug mode.

### 15. **Library Lookup (`lookup_library_in` Function):**
   - Looks up a library cell based on a hash.

### 16. **Overall:**
   - The code represents a complex and feature-rich virtual machine for executing smart contracts.
   - Gas management, control flow, and library handling are central aspects.
   - Cryptographic operations and gas pricing play a significant role in resource management.
   - The VM is designed to handle a variety of operations with a focus on efficiency and security.

***

Certainly, let's delve into the details of the relevant structures in the provided code.

### 1. `GasLimits` Struct:

```cpp
struct GasLimits {
  static constexpr long long infty = (1ULL << 63) - 1;
  long long gas_max, gas_limit, gas_credit, gas_remaining, gas_base;
  GasLimits() : gas_max(infty), gas_limit(infty), gas_credit(0), gas_remaining(infty), gas_base(infty) {
  }
  // ... (methods described in previous responses)
};
```

**Description:**
- Manages gas limits and consumption during VM execution.
- Represents the maximum, limit, credit, remaining, and base gas values.
- Provides methods to set limits, change base, consume gas, and check gas conditions.

### 2. `CommittedState` Struct:

```cpp
struct CommittedState {
  Ref<vm::Cell> c4, c5;
  bool committed{false};
};
```

**Description:**
- Represents a state with committed changes.
- Holds references to committed cells (`c4` and `c5`).
- Includes a flag (`committed`) indicating whether changes are committed.

### 3. `ParentVmState` Struct:

```cpp
struct ParentVmState {
  VmState state;
  bool return_data, return_actions, return_gas, isolate_gas;
  int ret_vals;
};
```

**Description:**
- Represents the state of a parent VM when running a child VM.
- Contains a `VmState` object (`state`) representing the parent VM state.
- Flags (`return_data`, `return_actions`, `return_gas`, `isolate_gas`) specify the type of information to return from the child VM.
- `ret_vals` indicates the number of return values expected.

### 4. `GasLimits` Class Methods (Continued):

```cpp
class GasLimits {
  // ... (previously described methods)
};
```

**Continued Methods:**
- Methods for gas limit manipulation, consumption, and exception handling.
- Ensure proper gas usage and maintain gas-related conditions.

### 5. `ControlRegs` Struct:

```cpp
struct ControlRegs {
  Ref<Continuation> c[4];
  Ref<Cell> d[4];
  Ref<Tuple> c7;
  // ... (methods not explicitly shown)
};
```

**Description:**
- Holds control registers and special registers for VM control flow.
- `c[0]` to `c[3]` represent continuations, and `d[0]` to `d[3]` represent data cells.
- `c7` holds additional control information.

### 6. `VmState` Class:

```cpp
class VmState final : public VmStateInterface {
  // ... (previously described members and methods)
};
```

**Description:**
- Main class representing the state of the virtual machine.
- Manages code execution, gas consumption, control flow, and stack operations.
- Contains instances of other structs and classes for comprehensive VM functionality.

These structures collectively contribute to the organization and functionality of the TON Blockchain Library's virtual machine. If you have specific questions about any particular part or method, feel free to ask.



***

Certainly, let's go through the primary classes in the provided code and explain their functionality in detail.

### 1. `GasLimits` Class:

```cpp
class GasLimits {
  // ... (previously described members and methods)
};
```

**Description:**
- **Purpose:** Manages gas limits and consumption during VM execution.
- **Members:**
  - `gas_max`: Maximum gas allowed.
  - `gas_limit`: Gas limit for the current operation.
  - `gas_credit`: Gas credited to the current operation.
  - `gas_remaining`: Remaining gas for the current operation.
  - `gas_base`: Base gas value for calculations.
- **Methods:**
  - `gas_consumed()`: Calculates the gas consumed so far.
  - `set_limits()`: Sets new gas limits.
  - `change_base()`: Changes the base gas value.
  - `change_limit()`: Changes the gas limit.
  - `consume()`: Consumes a specified amount of gas.
  - `try_consume()`: Tries to consume gas and returns success.
  - `gas_exception()`: Throws a gas exception if conditions are not met.
  - `consume_chk()`: Consumes gas and throws an exception if unsuccessful.
  - `check()`: Checks if gas remaining is non-negative.
  - `final_ok()`: Checks if the final gas state is valid.
  
### 2. `CommittedState` Struct:

```cpp
struct CommittedState {
  Ref<vm::Cell> c4, c5;
  bool committed{false};
};
```

**Description:**
- **Purpose:** Represents a state with committed changes.
- **Members:**
  - `c4`, `c5`: References to committed cells.
  - `committed`: Flag indicating whether changes are committed.

### 3. `ParentVmState` Struct:

```cpp
struct ParentVmState {
  VmState state;
  bool return_data, return_actions, return_gas, isolate_gas;
  int ret_vals;
};
```

**Description:**
- **Purpose:** Represents the state of a parent VM when running a child VM.
- **Members:**
  - `state`: `VmState` object representing the parent VM state.
  - `return_data`, `return_actions`, `return_gas`, `isolate_gas`: Flags specifying the type of information to return from the child VM.
  - `ret_vals`: Number of return values expected.

### 4. `ControlRegs` Struct:

```cpp
struct ControlRegs {
  Ref<Continuation> c[4];
  Ref<Cell> d[4];
  Ref<Tuple> c7;
  // ... (methods not explicitly shown)
};
```

**Description:**
- **Purpose:** Holds control registers and special registers for VM control flow.
- **Members:**
  - `c[0]` to `c[3]`: Continuations representing control flow.
  - `d[0]` to `d[3]`: Data cells for control flow.
  - `c7`: Tuple holding additional control information.

### 5. `VmState` Class (Continued):

```cpp
class VmState final : public VmStateInterface {
  // ... (previously described members and methods)
};
```

**Description:**
- **Purpose:** Main class representing the state of the virtual machine.
- **Members:**
  - Various members for managing code execution, gas consumption, control flow, and stack operations.
  - Includes instances of other structs and classes for comprehensive VM functionality.
- **Methods:**
  - Numerous methods for stack manipulation, gas consumption, control flow operations, and more.
  - Manages VM state, handles exceptions, and supports cryptographic operations.

### 6. `DispatchTable`:

```cpp
struct DispatchTable {
  // ... (not explicitly shown)
};
```

**Description:**
- **Purpose:** Not explicitly shown, likely contains a table of VM instruction handlers.
- **Note:** The details of this class are not provided in the snippet.

### 7. `VmLog` Class:

```cpp
class VmLog {
  // ... (not explicitly shown)
};
```

**Description:**
- **Purpose:** Manages logging of VM operations for debugging and analysis.
- **Note:** The details of this class are not provided in the snippet.

### 8. `Gas Pricing Constants`:

```cpp
class VmState {
  enum {
    // ... (various gas pricing constants)
  };
};
```

**Description:**
- **Purpose:** Defines gas pricing constants for various VM operations.
- **Note:** The constants represent the cost of executing different VM instructions.

These classes and structures collectively form the core components of the TON Blockchain Library's virtual machine, providing functionality for gas management, control flow, logging, and state representation. If you have specific questions about any particular part or method, feel free to ask.

***


The provided code snippet is quite extensive, and providing an explanation for every method of the `VmState` class in detail would be too extensive for this platform. However, I can give you an overview of the main methods and their purposes. If you have specific methods or areas of the code that you would like detailed explanations for, please let me know.

Here is a high-level overview of some key methods in the `VmState` class:

### 1. `VmState` Class:

#### Constructor Overloads:

- **`VmState()`:** Default constructor.
- **`VmState(Ref<CellSlice> _code)`:** Constructor with initial code.
- **`VmState(Ref<CellSlice> _code, Ref<Stack> _stack, ... )`:** Constructor with various parameters including stack, gas limits, etc.
- **`VmState(Ref<Cell> code_cell, Args&&... args)`:** Constructor with a code cell.

#### Gas Management:

- **`set_gas_limits(long long _max, long long _limit, long long _credit = 0)`:** Sets gas limits.
- **`consume_gas_chk(long long amount)`:** Consumes gas with a check for sufficiency.
- **`consume_gas(long long amount)`:** Consumes gas without a check (used based on global version).
- **`force_cp(int new_cp)`:** Forces a change in the code page.

#### Control Flow:

- **`init_cp(int new_cp)`:** Initializes the code page.
- **`set_cp(int new_cp)`:** Sets the code page.
- **`get_cp() const`:** Gets the current code page.
- **`call(Ref<Continuation> cont, ...)`:** Initiates a function call.
- **`jump(Ref<Continuation> cont, ...)`:** Initiates a jump to a continuation.
- **`ret(...)`:** Initiates a return from a function.
- **`repeat(Ref<Continuation> body, Ref<Continuation> after, long long count)`:** Repeats a block of code.
- **`again(Ref<Continuation> body)`:** Repeats a block indefinitely.
- **`until(Ref<Continuation> body, Ref<Continuation> after)`:** Repeats a block until a condition is met.
- **`loop_while(Ref<Continuation> cond, Ref<Continuation> body, Ref<Continuation> after)`:** Loops while a condition is true.

#### Stack and Data Manipulation:

- **`get_stack()`:** Gets a reference to the stack.
- **`get_log() const`:** Gets a reference to the VM log.
- **`get_c(unsigned idx) const`:** Gets a continuation at a specific index.
- **`set_c(unsigned idx, Ref<Continuation> val)`:** Sets a continuation at a specific index.
- **`get_d(unsigned idx) const`:** Gets a data cell at a specific index.
- **`set_d(unsigned idx, Ref<Cell> val)`:** Sets a data cell at a specific index.

#### Execution and Logging:

- **`run()`:** Executes the VM code.
- **`step()`:** Executes a single step of the VM code.
- **`get_steps_count() const`:** Gets the total number of steps executed.
- **`get_state_hash() const`:** Gets the hash of the VM state.
- **`get_final_state_hash(int exit_code) const`:** Gets the hash of the VM state after execution.

#### Library Management:

- **`register_library_collection(Ref<Cell> lib)`:** Registers a library collection.
- **`load_library(td::ConstBitPtr hash)`:** Loads a library based on its hash.

#### Child VM Handling:

- **`run_child_vm(VmState&& new_state, ...)`:** Runs a child VM.
- **`restore_parent_vm(int res)`:** Restores the parent VM state after running a child VM.

These are just summaries of the methods, and the actual implementation details can be quite intricate. If you have specific methods or areas you're interested in, feel free to ask for more detailed explanations.

***


Sure, let's take a closer look at some key methods in the `VmState` class. Due to the length of the code and the detailed nature of the methods, I'll focus on a few representative ones:

### 1. `VmState` Class:

#### Constructor Overloads:

- **`VmState()`:**
  - **Description:** Default constructor.
  - **Purpose:** Initializes a `VmState` object with default values.

- **`VmState(Ref<CellSlice> _code)`:**
  - **Description:** Constructor with initial code.
  - **Parameters:**
    - `_code`: Initial code slice.
  - **Purpose:** Initializes a `VmState` object with the provided code slice.

- **`VmState(Ref<CellSlice> _code, Ref<Stack> _stack, ... )`:**
  - **Description:** Constructor with various parameters including stack, gas limits, etc.
  - **Parameters:**
    - `_code`: Initial code slice.
    - `_stack`: Initial stack.
    - `...`: Gas limits, flags, data cell, log, libraries, and initialization tuple.
  - **Purpose:** Initializes a `VmState` object with the specified parameters.

- **`VmState(Ref<Cell> code_cell, Args&&... args)`:**
  - **Description:** Constructor with a code cell.
  - **Parameters:**
    - `code_cell`: Initial code cell.
    - `Args&&... args`: Additional arguments.
  - **Purpose:** Initializes a `VmState` object with the given code cell and additional arguments.

#### Gas Management:

- **`set_gas_limits(long long _max, long long _limit, long long _credit = 0)`:**
  - **Description:** Sets gas limits.
  - **Parameters:**
    - `_max`: Maximum gas allowed.
    - `_limit`: Gas limit for the current operation.
    - `_credit`: Gas credited to the current operation.
  - **Purpose:** Configures gas limits for the VM.

- **`consume_gas_chk(long long amount)`:**
  - **Description:** Consumes gas with a check for sufficiency.
  - **Parameters:**
    - `amount`: Amount of gas to consume.
  - **Purpose:** Consumes gas and throws an exception if the operation exceeds the remaining gas.

#### Control Flow:

- **`init_cp(int new_cp)`:**
  - **Description:** Initializes the code page.
  - **Parameters:**
    - `new_cp`: New code page value.
  - **Purpose:** Initializes the VM's code page.

- **`set_cp(int new_cp)`:**
  - **Description:** Sets the code page.
  - **Parameters:**
    - `new_cp`: New code page value.
  - **Purpose:** Sets the VM's code page.

- **`call(Ref<Continuation> cont, ...)`:**
  - **Description:** Initiates a function call.
  - **Parameters:**
    - `cont`: Continuation to call.
    - `...`: Additional parameters.
  - **Purpose:** Initiates a function call with the specified continuation and parameters.

#### Stack and Data Manipulation:

- **`get_stack()`:**
  - **Description:** Gets a reference to the stack.
  - **Purpose:** Provides access to the VM's stack.

- **`get_log() const`:**
  - **Description:** Gets a reference to the VM log.
  - **Purpose:** Provides access to the VM's log.

- **`get_c(unsigned idx) const`:**
  - **Description:** Gets a continuation at a specific index.
  - **Parameters:**
    - `idx`: Index of the continuation.
  - **Purpose:** Retrieves a continuation from the control registers.

- **`set_c(unsigned idx, Ref<Continuation> val)`:**
  - **Description:** Sets a continuation at a specific index.
  - **Parameters:**
    - `idx`: Index of the continuation.
    - `val`: Continuation to set.
  - **Purpose:** Sets a continuation in the control registers.

#### Execution and Logging:

- **`run()`:**
  - **Description:** Executes the VM code.
  - **Purpose:** Runs the VM code until completion.

- **`step()`:**
  - **Description:** Executes a single step of the VM code.
  - **Purpose:** Advances the VM by one step in execution.

- **`get_steps_count() const`:**
  - **Description:** Gets the total number of steps executed.
  - **Purpose:** Retrieves the count of executed steps.

- **`get_state_hash() const`:**
  - **Description:** Gets the hash of the VM state.
  - **Purpose:** Calculates and returns the hash of the current VM state.

#### Library Management:

- **`register_library_collection(Ref<Cell> lib)`:**
  - **Description:** Registers a library collection.
  - **Parameters:**
    - `lib`: Library collection to register.
  - **Purpose:** Adds a library collection to the VM.

- **`load_library(td::ConstBitPtr hash)`:**
  - **Description:** Loads a library based on its hash.
  - **Parameters:**
    - `hash`: Hash of the library to load.
  - **Purpose:** Retrieves and loads a library based on its hash.

#### Child VM Handling:

- **`run_child_vm(VmState&& new_state, ...)`:**
  - **Description:** Runs a child VM.
  - **Parameters:**
    - `new_state`: State of the child VM.
    - `...`: Flags and options for return values.
  - **Purpose:** Executes a child VM within the context of the parent VM.

- **`restore_parent_vm(int res)`:**
  - **Description:** Restores the parent VM state after running a child VM.
  - **Parameters:**
    - `res`: Result code from the child VM.
  - **Purpose:** Restores the parent VM state based on the result of the child VM execution.

These are summaries of the key methods in the `VmState` class. If you have specific methods or areas you're interested in, feel free to ask for more detailed explanations.

Sure, let's take a closer look at some key methods in the `VmState` class. Due to the length of the code and the detailed nature of the methods, I'll focus on a few representative ones:

### 1. `VmState` Class:

#### Constructor Overloads:

- **`VmState()`:**
  - **Description:** Default constructor.
  - **Purpose:** Initializes a `VmState` object with default values.

- **`VmState(Ref<CellSlice> _code)`:**
  - **Description:** Constructor with initial code.
  - **Parameters:**
    - `_code`: Initial code slice.
  - **Purpose:** Initializes a `VmState` object with the provided code slice.

- **`VmState(Ref<CellSlice> _code, Ref<Stack> _stack, ... )`:**
  - **Description:** Constructor with various parameters including stack, gas limits, etc.
  - **Parameters:**
    - `_code`: Initial code slice.
    - `_stack`: Initial stack.
    - `...`: Gas limits, flags, data cell, log, libraries, and initialization tuple.
  - **Purpose:** Initializes a `VmState` object with the specified parameters.

- **`VmState(Ref<Cell> code_cell, Args&&... args)`:**
  - **Description:** Constructor with a code cell.
  - **Parameters:**
    - `code_cell`: Initial code cell.
    - `Args&&... args`: Additional arguments.
  - **Purpose:** Initializes a `VmState` object with the given code cell and additional arguments.

#### Gas Management:

- **`set_gas_limits(long long _max, long long _limit, long long _credit = 0)`:**
  - **Description:** Sets gas limits.
  - **Parameters:**
    - `_max`: Maximum gas allowed.
    - `_limit`: Gas limit for the current operation.
    - `_credit`: Gas credited to the current operation.
  - **Purpose:** Configures gas limits for the VM.

- **`consume_gas_chk(long long amount)`:**
  - **Description:** Consumes gas with a check for sufficiency.
  - **Parameters:**
    - `amount`: Amount of gas to consume.
  - **Purpose:** Consumes gas and throws an exception if the operation exceeds the remaining gas.

#### Control Flow:

- **`init_cp(int new_cp)`:**
  - **Description:** Initializes the code page.
  - **Parameters:**
    - `new_cp`: New code page value.
  - **Purpose:** Initializes the VM's code page.

- **`set_cp(int new_cp)`:**
  - **Description:** Sets the code page.
  - **Parameters:**
    - `new_cp`: New code page value.
  - **Purpose:** Sets the VM's code page.

- **`call(Ref<Continuation> cont, ...)`:**
  - **Description:** Initiates a function call.
  - **Parameters:**
    - `cont`: Continuation to call.
    - `...`: Additional parameters.
  - **Purpose:** Initiates a function call with the specified continuation and parameters.

#### Stack and Data Manipulation:

- **`get_stack()`:**
  - **Description:** Gets a reference to the stack.
  - **Purpose:** Provides access to the VM's stack.

- **`get_log() const`:**
  - **Description:** Gets a reference to the VM log.
  - **Purpose:** Provides access to the VM's log.

- **`get_c(unsigned idx) const`:**
  - **Description:** Gets a continuation at a specific index.
  - **Parameters:**
    - `idx`: Index of the continuation.
  - **Purpose:** Retrieves a continuation from the control registers.

- **`set_c(unsigned idx, Ref<Continuation> val)`:**
  - **Description:** Sets a continuation at a specific index.
  - **Parameters:**
    - `idx`: Index of the continuation.
    - `val`: Continuation to set.
  - **Purpose:** Sets a continuation in the control registers.

#### Execution and Logging:

- **`run()`:**
  - **Description:** Executes the VM code.
  - **Purpose:** Runs the VM code until completion.

- **`step()`:**
  - **Description:** Executes a single step of the VM code.
  - **Purpose:** Advances the VM by one step in execution.

- **`get_steps_count() const`:**
  - **Description:** Gets the total number of steps executed.
  - **Purpose:** Retrieves the count of executed steps.

- **`get_state_hash() const`:**
  - **Description:** Gets the hash of the VM state.
  - **Purpose:** Calculates and returns the hash of the current VM state.

#### Library Management:

- **`register_library_collection(Ref<Cell> lib)`:**
  - **Description:** Registers a library collection.
  - **Parameters:**
    - `lib`: Library collection to register.
  - **Purpose:** Adds a library collection to the VM.

- **`load_library(td::ConstBitPtr hash)`:**
  - **Description:** Loads a library based on its hash.
  - **Parameters:**
    - `hash`: Hash of the library to load.
  - **Purpose:** Retrieves and loads a library based on its hash.

#### Child VM Handling:

- **`run_child_vm(VmState&& new_state, ...)`:**
  - **Description:** Runs a child VM.
  - **Parameters:**
    - `new_state`: State of the child VM.
    - `...`: Flags and options for return values.
  - **Purpose:** Executes a child VM within the context of the parent VM.

- **`restore_parent_vm(int res)`:**
  - **Description:** Restores the parent VM state after running a child VM.
  - **Parameters:**
    - `res`: Result code from the child VM.
  - **Purpose:** Restores the parent VM state based on the result of the child VM execution.

These are summaries of the key methods in the `VmState` class. If you have specific methods or areas you're interested in, feel free to ask for more detailed explanations.
