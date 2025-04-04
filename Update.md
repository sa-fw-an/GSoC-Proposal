<!--markdownlint-disable-->
#### Project Overview
It's important to note that the Program Engine and Masonry (the block programming interface) are designed as independent modules with clear separation of concerns. While they interact through a shared program tree representation, they don't dictate each other's implementation details. This architectural decision ensures flexibility and maintainability by allowing each module to evolve independently.

#### Alignment with Music Blocks Architecture
It's important to emphasize that the engine and masonry components are independent modules with well-defined boundaries. While they share the program tree (either directly or through a proxy interface), neither dictates the other's implementation. My focus will be on creating an engine that operates solely on the program tree it receives, maintaining a clean separation of concerns that allows both components to evolve independently while preserving their integration points.

#### Technical Specifications
The AST will be explicitly separated from the UI program tree:
   
- AST contains only execution-relevant information
- The engine will be implementation-agnostic, operating on a generic programming framework model
- The engine will receive program representations without making assumptions about their visual/UI origins
- Translation between the masonry (visual) layer and engine layer will occur through clearly defined interfaces

#### Debugging Framework

A key aspect of the Program Engine will be a comprehensive debugging framework that enables users to understand and troubleshoot their programs:

1. **Breakpoint Architecture**:
   - Integration of breakpoint capability directly into the execution model
   - Support for conditional breakpoints that trigger only when specific conditions are met
   - Efficient implementation that minimizes performance impact when debugging is not active

2. **Program Flow Control**:
   - Execution state snapshotting to enable pausing and resuming execution
   - Implementation of execution step granularity controls (step into, over, out)
   - Careful management of execution context during debugging to maintain program integrity

3. **State Visualization and Modification**:
   - Read and write access to program variables during debugging pauses
   - Call stack introspection to show execution path
   - Ability to modify program state during debugging pauses to test alternative scenarios

The debugging framework will be implemented as a core feature of the engine, not as an afterthought. This approach ensures that debugging capabilities are deeply integrated and provide meaningful insights into program behavior.


#### Debugging Support

To aid development and user experience, the engine will include comprehensive debugging capabilities focused on helping users understand and troubleshoot their programs:

1. **Interactive Debugging Tools**:
   - **Breakpoint System**: Implementation of configurable breakpoints that can be set at any executable node in the program tree
   - **Step Execution Controls**: Ability to execute programs one instruction at a time with "step into" (descend into function calls), "step over" (execute function calls without stepping into them), and "step out" (execute until current function returns)
   - **Execution Visualization**: Real-time highlighting of currently executing blocks to help users visually track program flow

2. **Program State Inspection**:
   - **Variable Inspector**: Interactive tool for examining all variables in the current scope during execution pauses
   - **Call Stack Viewer**: Display of the current execution stack, showing nested function calls and their origins
   - **Expression Evaluation**: Ability to evaluate arbitrary expressions in the context of the current execution state
   - **Watch Expressions**: Support for persistent expressions that are evaluated automatically at each execution step

3. **Error Handling and Recovery**:
   - Detailed error reporting, including error messages, stack traces, and context information
   - Recovery mechanisms for non-fatal errors, allowing programs to continue execution where possible
   - Suggested fixes for common programming mistakes

4. **Performance Analysis**:
   - Execution time tracking for different program segments
   - Identification of potential performance bottlenecks
   - Resource utilization monitoring
