# Skill: Fuzzing

> Automated testing to discover bugs through random/semi-random input generation, derived from Google's OSS-Fuzz methodology.

## When to Use

- Testing parsers and input handlers
- Finding memory corruption bugs
- Testing protocol implementations
- Security testing of libraries
- CI/CD security integration

## Prerequisites

- Code compiled with instrumentation
- Sanitizers enabled (ASan, MSan, UBSan)
- Initial corpus (seed inputs)
- Fuzzing infrastructure
- Bug tracking system

## Steps

### 1. Identify Fuzz Targets
```
HIGH-VALUE TARGETS:
□ Parsers (file formats, protocols, data structures)
□ Deserializers (JSON, XML, protobuf, etc.)
□ Compression/decompression
□ Cryptographic operations
□ Network protocol handlers
□ Command-line argument parsing
□ Image/media processing
□ Any code handling untrusted input
```

### 2. Write Fuzz Target
```
FUZZ TARGET STRUCTURE:

extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    // 1. Convert fuzzer input to appropriate format
    // 2. Call target function
    // 3. Return 0 (or let sanitizer catch bugs)
    return 0;
}

REQUIREMENTS:
□ Deterministic (same input = same behavior)
□ No global state leakage between runs
□ No intentional crashes
□ Fast execution (< 100ms per input)
□ Avoid memory allocation in hot path
```

### 3. Build with Instrumentation
```bash
# Clang with coverage + sanitizers
export CC=clang
export CXX=clang++
export CFLAGS="-fsanitize=address,fuzzer-no-link -g"
export CXXFLAGS="-fsanitize=address,fuzzer-no-link -g"

# Build the target
make

# Link fuzz target
clang++ -fsanitize=address,fuzzer fuzz_target.cc -o fuzzer
```

### 4. Create Seed Corpus
```
CORPUS SOURCES:
□ Valid inputs (real-world examples)
□ Minimal examples
□ Edge cases
□ Previously found bugs (regression)
□ Format specification examples

CORPUS QUALITY:
□ Cover different code paths
□ Include boundary values
□ Include empty/minimal inputs
□ Include maximum-size inputs
```

### 5. Run Fuzzer
```bash
# Basic run
./fuzzer corpus_dir/

# With options
./fuzzer corpus_dir/ \
    -max_len=10000 \           # Max input size
    -timeout=10 \              # Per-input timeout
    -jobs=4 \                  # Parallel jobs
    -workers=4 \               # Worker processes
    -dict=format.dict \        # Dictionary
    -artifact_prefix=crashes/  # Crash output
```

### 6. Triage Crashes
```
FOR EACH CRASH:

1. Reproduce:
   ./fuzzer crash_file

2. Minimize:
   ./fuzzer -minimize_crash=1 crash_file

3. Analyze:
   - Read sanitizer output
   - Identify root cause
   - Determine severity

4. Report:
   - File bug
   - Include minimized reproducer
   - Include sanitizer output
```

### 7. Improve Coverage
```
COVERAGE IMPROVEMENT:
□ Review uncovered code
□ Add dictionary entries
□ Add more seed inputs
□ Create structure-aware harness
□ Use custom mutators (if needed)

MONITOR:
□ Coverage growth
□ Corpus size
□ Execution speed
□ New edges found
```

## Fuzzing Patterns

### Basic Fuzz Target
```cpp
// Fuzzing a parser
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    parse_format(data, size);
    return 0;
}
```

### Structure-Aware Fuzzing
```cpp
// Using protobuf for structured input
#include "fuzz_input.pb.h"
#include "libprotobuf-mutator/src/libfuzzer/libfuzzer_macro.h"

DEFINE_PROTO_FUZZER(const FuzzInput& input) {
    process_structured_input(input);
}
```

### API Fuzzing
```cpp
// Fuzzing API sequences
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    FuzzedDataProvider fdp(data, size);
    
    auto obj = create_object();
    
    while (fdp.remaining_bytes() > 0) {
        int action = fdp.ConsumeIntegral<int>() % 5;
        switch (action) {
            case 0: obj.method_a(fdp.ConsumeIntegral<int>()); break;
            case 1: obj.method_b(fdp.ConsumeString(100)); break;
            // ... more actions
        }
    }
    return 0;
}
```

## OSS-Fuzz Integration

### Project Structure
```
oss-fuzz/projects/myproject/
├── Dockerfile          # Build environment
├── build.sh           # Build script
├── project.yaml       # Project config
└── fuzz_target.cc     # Fuzz targets
```

### Dockerfile
```dockerfile
FROM gcr.io/oss-fuzz-base/base-builder
RUN apt-get update && apt-get install -y cmake
RUN git clone --depth 1 https://github.com/example/myproject
COPY build.sh $SRC/
COPY *.cc $SRC/
WORKDIR $SRC/myproject
```

### build.sh
```bash
#!/bin/bash -eu
mkdir build && cd build
cmake .. -DCMAKE_C_COMPILER=$CC -DCMAKE_CXX_COMPILER=$CXX
make -j$(nproc)

$CXX $CXXFLAGS -I../include \
    $SRC/fuzz_target.cc \
    -o $OUT/fuzz_target \
    $LIB_FUZZING_ENGINE ./libmyproject.a
```

### project.yaml
```yaml
homepage: "https://github.com/example/myproject"
primary_contact: "maintainer@example.com"
language: c++
sanitizers:
  - address
  - memory
  - undefined
fuzzing_engines:
  - libfuzzer
  - afl
  - honggfuzz
```

## Bug Types Found

| Bug Type | Sanitizer | Example |
|----------|-----------|---------|
| Buffer overflow | ASan | Heap/stack out-of-bounds |
| Use-after-free | ASan | Dangling pointer dereference |
| Memory leak | LSan | Unreleased allocation |
| Uninitialized read | MSan | Using uninitialized memory |
| Integer overflow | UBSan | Signed overflow |
| Null dereference | ASan/UBSan | Null pointer access |
| Division by zero | UBSan | Divide by zero |

## Examples

### Good: Comprehensive Harness
```cpp
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    // Reject trivially small inputs
    if (size < 4) return 0;
    
    // Limit maximum size to avoid timeouts
    if (size > 1024 * 1024) return 0;
    
    // Create non-null-terminated copy if needed
    std::vector<uint8_t> buf(data, data + size);
    
    // Call target
    int result = parse_data(buf.data(), buf.size());
    
    // Check invariants (optional)
    assert(result == 0 || result == 1);
    
    return 0;
}
```

### Good: Dictionary File
```
# format.dict - tokens for format fuzzing
"<html>"
"</html>"
"<script>"
"href="
"onclick="
"\x00"
"\xff\xff"
```

## Anti-patterns

### ❌ No Sanitizers
```
DON'T: Fuzz without sanitizers
WHY: Bugs won't be detected, just silent corruption
FIX: Always use ASan/MSan/UBSan
```

### ❌ Slow Targets
```
DON'T: Fuzz targets that take >100ms
WHY: Dramatically reduces executions per second
FIX: Optimize hot paths, mock slow dependencies
```

### ❌ Non-Deterministic Targets
```
DON'T: Use random(), time(), global state
WHY: Can't reproduce crashes
FIX: Make targets deterministic
```

### ❌ Empty Corpus
```
DON'T: Start with no seed inputs
WHY: Fuzzer wastes time generating valid structure
FIX: Provide representative seed corpus
```

## Metrics

- Executions per second
- Coverage (lines, branches, edges)
- Corpus size
- Bugs found
- Time to find first bug
- Coverage growth rate

## References

- OSS-Fuzz documentation
- libFuzzer documentation
- AFL++ documentation
- Google Testing Blog (fuzzing posts)
