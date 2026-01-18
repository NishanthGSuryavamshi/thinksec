# Skill: Sanitizer Usage

> Using runtime sanitizers to detect memory errors, data races, and undefined behavior, derived from Google's sanitizer methodology.

## When to Use

- Developing C/C++ code
- Running test suites
- Debugging memory issues
- Finding concurrency bugs
- CI/CD security testing

## Prerequisites

- Clang or GCC compiler
- Source code access
- Test suite or workload
- Understanding of bug types

## Steps

### 1. Choose Sanitizers
```
SANITIZER SELECTION:

AddressSanitizer (ASan):
  - Memory errors: buffer overflow, use-after-free
  - Use for: Most development, fuzzing
  - Overhead: ~2x slowdown, ~2-3x memory

MemorySanitizer (MSan):
  - Uninitialized memory reads
  - Use for: Finding subtle bugs
  - Overhead: ~3x slowdown
  - Note: Requires all deps built with MSan

ThreadSanitizer (TSan):
  - Data races, deadlocks
  - Use for: Concurrent code
  - Overhead: ~5-15x slowdown, ~5-10x memory

UndefinedBehaviorSanitizer (UBSan):
  - Undefined behavior (integer overflow, bad casts)
  - Use for: All code, low overhead
  - Overhead: Minimal for most checks

LeakSanitizer (LSan):
  - Memory leaks
  - Use for: Finding leaks
  - Note: Included with ASan by default
```

### 2. Build with Sanitizers
```bash
# AddressSanitizer
clang -fsanitize=address -g -O1 code.c -o code

# MemorySanitizer
clang -fsanitize=memory -g -O1 code.c -o code

# ThreadSanitizer
clang -fsanitize=thread -g -O1 code.c -o code

# UndefinedBehaviorSanitizer
clang -fsanitize=undefined -g -O1 code.c -o code

# Combined (ASan + UBSan)
clang -fsanitize=address,undefined -g -O1 code.c -o code

# Recommended flags
-g              # Debug info for readable reports
-O1             # Optimization for realistic behavior
-fno-omit-frame-pointer  # Better stack traces
```

### 3. Configure Options
```bash
# ASan options
export ASAN_OPTIONS="detect_leaks=1:detect_stack_use_after_return=1"

# MSan options
export MSAN_OPTIONS="poison_in_dtor=1"

# TSan options
export TSAN_OPTIONS="history_size=7:second_deadlock_stack=1"

# UBSan options
export UBSAN_OPTIONS="print_stacktrace=1:halt_on_error=1"

# Common options
abort_on_error=1          # Abort on first error
symbolize=1               # Symbolize stack traces
detect_leaks=1            # Enable leak detection
halt_on_error=1           # Stop on first error
```

### 4. Run Tests
```bash
# Run with sanitizers
./sanitized_binary

# Run test suite
make test

# Run with specific options
ASAN_OPTIONS="detect_leaks=1" ./my_tests
```

### 5. Interpret Reports
```
ASAN REPORT STRUCTURE:

=================================================================
==12345==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x...
[Location of error]
    #0 0x... in function_name file.c:123
    #1 0x... in caller_function file.c:456
    
[Memory access details]
0x... is located 4 bytes to the right of 100-byte region [0x...,0x...)
allocated by thread T0 here:
    #0 0x... in malloc
    #1 0x... in allocator_function file.c:789
```

### 6. Fix Bugs
```
FOR EACH REPORT:
1. Read error type (overflow, UAF, race, etc.)
2. Examine stack trace (where error occurred)
3. Check allocation stack (where memory came from)
4. Understand the bug
5. Fix root cause
6. Verify fix (re-run with sanitizers)
```

## Sanitizer Details

### AddressSanitizer (ASan)
```
DETECTS:
□ Heap buffer overflow
□ Stack buffer overflow
□ Global buffer overflow
□ Use-after-free
□ Use-after-return (with flag)
□ Use-after-scope
□ Double-free
□ Memory leaks (via LSan)

HOW IT WORKS:
- Shadow memory tracks validity of each byte
- Redzones around allocations detect overflow
- Quarantine delays freeing to detect UAF
```

### MemorySanitizer (MSan)
```
DETECTS:
□ Use of uninitialized memory

HOW IT WORKS:
- Shadow memory tracks initialization state
- Propagates "uninit" through operations
- Reports when uninit affects control flow or output

REQUIREMENTS:
- All code must be MSan-instrumented
- Standard library: use msan-instrumented libc++
```

### ThreadSanitizer (TSan)
```
DETECTS:
□ Data races
□ Deadlocks
□ Thread leaks
□ Mutex misuse

HOW IT WORKS:
- Vector clocks track happens-before
- Shadow state per memory location
- Detects conflicting accesses
```

### UndefinedBehaviorSanitizer (UBSan)
```
DETECTS:
□ Signed integer overflow
□ Unsigned integer overflow (optional)
□ Division by zero
□ Null pointer dereference
□ Invalid type cast
□ Misaligned pointer
□ Out-of-bounds array index
□ Invalid bool value
□ Invalid enum value

CHECKS TO ENABLE:
-fsanitize=undefined              # Most checks
-fsanitize=integer                # All integer checks
-fsanitize=nullability            # Null checks
```

## CMake Integration

```cmake
# Option to enable sanitizers
option(ENABLE_SANITIZERS "Enable sanitizers" OFF)

if(ENABLE_SANITIZERS)
    set(SANITIZER_FLAGS "-fsanitize=address,undefined -fno-omit-frame-pointer")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${SANITIZER_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${SANITIZER_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${SANITIZER_FLAGS}")
endif()
```

## CI/CD Integration

```yaml
# GitHub Actions example
jobs:
  sanitizers:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        sanitizer: [address, undefined, thread]
    steps:
      - uses: actions/checkout@v3
      - name: Build with ${{ matrix.sanitizer }} sanitizer
        run: |
          cmake -B build \
            -DCMAKE_C_COMPILER=clang \
            -DCMAKE_CXX_COMPILER=clang++ \
            -DCMAKE_C_FLAGS="-fsanitize=${{ matrix.sanitizer }} -g" \
            -DCMAKE_CXX_FLAGS="-fsanitize=${{ matrix.sanitizer }} -g"
          cmake --build build
      - name: Run tests
        run: ctest --test-dir build
        env:
          ASAN_OPTIONS: detect_leaks=1
          UBSAN_OPTIONS: halt_on_error=1
```

## Examples

### ASan: Buffer Overflow
```c
// Bug
int arr[10];
arr[10] = 42;  // Out of bounds!

// ASan output
==12345==ERROR: AddressSanitizer: stack-buffer-overflow
WRITE of size 4 at 0x7ffd12345678
    #0 0x... in main test.c:3
```

### MSan: Uninitialized Read
```c
// Bug
int x;
if (x) {  // Using uninitialized!
    do_something();
}

// MSan output
==12345==WARNING: MemorySanitizer: use-of-uninitialized-value
    #0 0x... in main test.c:3
```

### TSan: Data Race
```c
// Bug (no synchronization)
int counter;
void* thread_func(void* arg) {
    counter++;  // Race!
    return NULL;
}

// TSan output
==12345==WARNING: ThreadSanitizer: data race
  Write of size 4 at 0x... by thread T1:
    #0 thread_func test.c:3
  Previous write of size 4 at 0x... by thread T2:
    #0 thread_func test.c:3
```

## Anti-patterns

### ❌ Production Builds with Sanitizers
```
DON'T: Deploy sanitized builds to production
WHY: Severe performance overhead
FIX: Sanitizers for testing/CI only
```

### ❌ Ignoring Reports
```
DON'T: Suppress warnings without investigation
WHY: Real bugs get missed
FIX: Investigate every report, fix or document false positives
```

### ❌ MSan with Uninstrumented Deps
```
DON'T: Run MSan with system libraries
WHY: False positives from uninstrumented code
FIX: Build all dependencies with MSan
```

### ❌ No Debug Info
```
DON'T: Build without -g
WHY: Stack traces are unreadable
FIX: Always include debug info with sanitizers
```

## Metrics

- Test pass rate with sanitizers
- Bugs found by sanitizer type
- CI build time with sanitizers
- Coverage with sanitized tests

## References

- google/sanitizers wiki
- Clang sanitizer documentation
- AddressSanitizer paper (USENIX ATC 2012)
