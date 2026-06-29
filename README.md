# Contribution 1: Add ABI/SyscallABI Support for LoongArch64 + s390x

**Contribution Number:** 1  
**Student:** Angelika Szymanowska  
**Issue:** https://github.com/pwndbg/pwndbg/issues/2889  
**Status:** Phase VI - In Progress

----

## Why I Chose This Issue

I chose this issue because it combines Python development with low-level operating system concepts such as system calls and application binary interfaces (ABIs). As a cybersecurity student interested in reverse engineering, debugging, and system internals, I wanted to work on a contribution that would expose me to architecture-specific behavior in Linux systems.

This issue also provides an opportunity to learn how Pwndbg and Pwntools represent syscall conventions across different processor architectures. Through this contribution, I hope to strengthen my Python skills, improve my understanding of Linux system calls, and gain practical experience contributing to an open-source cybersecurity project.

---

## Understanding the Issue

### Problem Description

Issue #2889 requests ABI and SyscallABI support for LoongArch64 and s390x architectures.

Pwndbg relies on ABI definitions to determine how function arguments, syscall arguments, and sigreturn handlers are represented for a given architecture. These definitions are stored in architecture-specific lookup tables and are used throughout the debugger when resolving arguments and syscall information.

### Expected Behavior

Pwndbg should correctly recognize LoongArch64 and s390x architectures and provide ABI, syscall ABI, and sigreturn ABI definitions that can be used during debugging.

### Current Behavior

During investigation, I found that both LoongArch64 and s390x architecture support existed within Pwndbg at the architecture layer, but ABI support was incomplete.

The corresponding ABI, SyscallABI, and SigreturnABI definitions were missing from `pwndbg/lib/abi.py`.

As a result, Pwndbg could not fully resolve architecture-specific ABI information for these architectures.




### Affected Components

- `pwndbg/lib/abi.py`
- `pwndbg/aglib/arch.py`
- Related ABI and syscall handling logic

---

## Reproduction Process

### Environment Setup

- Windows 11
- Visual Studio Code
- Git Bash
- Python
- uv package manager
- Local clone of the Pwndbg repository

### Steps to Reproduce

1. Clone the Pwndbg repository.
2. Open `pwndbg/lib/abi.py`.
3. Locate `DEFAULT_ABIS`.
4. Locate `SYSCALL_ABIS`.
5. Locate `SIGRETURN_ABIS`.
6. Observe that ABI support for LoongArch64 and s390x is incomplete or missing.
7. Open `pwndbg/aglib/arch.py`.
8. Locate the `S390xArch` implementation.


### Reproduction Evidence

My investigation confirmed that:

- `S390xArch` is implemented in `pwndbg/aglib/arch.py`
- `super().__init__("s390x")` registers the architecture name as `s390x`
- ABI lookups rely on architecture identifiers generated from:

```python
default_abi_identifier = (self.ptrbits, self.name, "linux")
```

- `DEFAULT_ABIS` did not contain complete entries for LoongArch64 and s390x
- `SYSCALL_ABIS` did not contain complete entries for LoongArch64 and s390x
- `SIGRETURN_ABIS` did not contain complete entries for LoongArch64 and s390x

This prevented ABI resolution from succeeding for LoongArch64 and s390x.

---

## Solution Approach

### Analysis

I traced the ABI resolution flow from architecture initialization through the ABI lookup tables.

The architecture itself was already implemented and registered correctly. The missing functionality was located in `pwndbg/lib/abi.py`, where ABI definitions are stored and later retrieved by architecture-specific lookup keys.
Because ABI resolution relies on architecture-specific lookup keys, both LoongArch64 and s390x require corresponding entries in the ABI lookup tables for function, syscall, and sigreturn handling.

### Proposed Solution

Add LoongArch64 and s390x ABI definitions and register them within the ABI lookup tables.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:**

Pwndbg recognizes LoongArch64 and s390x architectures but lacks complete ABI definitions required for ABI resolution.

**Match:**

Existing architectures such as AArch64, RISC-V, MIPS, and PowerPC already provide ABI, SyscallABI, and SigreturnABI implementations that can be used as reference models.

**Plan:**

1. Review existing ABI definitions in `pwndbg/lib/abi.py`.
2. Review equivalent definitions in Pwntools.
3. Research s390x calling conventions and syscall conventions.
4. Add ABI definitions for LoongArch64 and s390x.
5. Add SyscallABI definitions for LoongArch64 and s390x.
6. Add SigreturnABI definitions for LoongArch64 and s390x.
7. Register both architectures in:
   - `DEFAULT_ABIS`
   - `SYSCALL_ABIS`
   - `SIGRETURN_ABIS`
8. Validate the modified file compiles successfully.


**Implement:**

Implemented the following additions:

```python
linux_loongarch64 = ABI(("a0", "a1", "a2", "a3", "a4", "a5", "a6", "a7"), 8, 0)

linux_loongarch64_syscall = SyscallABI(
    ("a7", "a0", "a1", "a2", "a3", "a4", "a5", "a6"),
    8,
    0,
)

linux_loongarch64_sigreturn = SigreturnABI(("a7",), 8, 0)

linux_s390x = ABI(("r2", "r3", "r4", "r5", "r6"), 8, 0)

linux_s390x_syscall = SyscallABI(
    ("r1", "r2", "r3", "r4", "r5", "r6"),
    8,
    0,
)

linux_s390x_sigreturn = SigreturnABI(("r1",), 8, 0)
```

Added corresponding entries to:

- `DEFAULT_ABIS`
- `SYSCALL_ABIS`
- `SIGRETURN_ABIS`

**Review:**

- [x] Follow project contribution guidelines
- [x] Follow project coding style
- [x] Ensure existing architectures remain unchanged
- [x] Verify file compiles successfully

**Evaluate:**

Initial validation completed successfully. Additional runtime testing may be required to verify behavior on actual LoongArch64 and s390x targets.

---

## Testing Strategy

### Unit Tests
- [x] Verify LoongArch64 ABI registration
- [x] Verify LoongArch64 SyscallABI registration
- [x] Verify LoongArch64 SigreturnABI registration
- [x] Verify s390x ABI registration
- [x] Verify s390x SyscallABI registration
- [x] Verify s390x SigreturnABI registration

### Integration Tests

- [ ] Validate architecture detection during runtime
- [ ] Validate syscall argument resolution
- [ ] Validate syscall name resolution

### Manual Testing

- Reviewed code changes using `git diff`.
- Verified only the intended ABI entries were modified.
- Created and pushed a commit to the `fix-issue-2889` branch.
- Installed project dependencies using `uv sync`.
- Attempted to run `./lint.sh`.
- Attempted to run `./unit-tests.sh`.

Linting and testing were blocked by Windows-specific environment limitations. The project requires Linux-based dependencies and build tools including `cmake`, `capstone`, and packages that are not currently available for the Windows platform.
## Implementation Notes

### Week 1 Progress

- Selected issue.
- Reviewed issue description and maintainer comments.
- Identified relevant source files.
- Began researching ABI and syscall handling in Pwndbg and Pwntools.
  
### Week 2 Progress

- Investigated architecture registration for s390x.
- Traced ABI lookup flow through `arch.py`.
- Compared Pwndbg ABI implementation against Pwntools.
- Researched LoongArch64 and s390x calling conventions and syscall conventions.
- Added `linux_loongarch64` ABI definition.
- Added `linux_loongarch64_syscall` definition.
- Added `linux_loongarch64_sigreturn` definition.
- Added `linux_s390x` ABI definition.
- Added `linux_s390x_syscall` definition.
- Added `linux_s390x_sigreturn` definition.
- Added LoongArch64 entries to:
  - `DEFAULT_ABIS`
  - `SYSCALL_ABIS`
  - `SIGRETURN_ABIS`
- Added s390x entries to:
  - `DEFAULT_ABIS`
  - `SYSCALL_ABIS`
  - `SIGRETURN_ABIS`
- Verified `pwndbg/lib/abi.py` compiles successfully.
- Created a local commit documenting the changes.

### Week 3 Progress

- Installed uv package manager.
- Created project virtual environment using uv sync.
- Attempted project linting.
- Attempted unit testing.
- Investigated Windows-specific dependency and platform limitations.
- Pushed implementation branch to GitHub.

### Week 4 Progress

- Reviewed the remaining Phase IV requirements and planned the pull request workflow.
- Revisited the implementation to verify the ABI additions and prepare for final validation.
- Reviewed the current code changes and confirmed the implementation is ready for final testing.
- Updated this README to reflect the current project status.

**Current Status:** In Progress

**Next Steps:**
- Complete testing in a Linux environment.
- Perform a final review of the implementation.
- Open a pull request to the upstream Pwndbg repository.
- Respond to maintainer feedback and iterate as needed.
  
### Code Changes

**Files modified:**

- pwndbg/lib/abi.py

  
**Branch:**

fix-issue-2889

**Repository:**

https://github.com/angelikakasia/pwndbg/tree/fix-issue-2889

**Key commits:**

f3663d2 - Add LoongArch64 and s390x syscall ABI mappings

**Approach decisions:**

- Reused existing ABI patterns from other supported architectures.
- Matched architecture identifier naming with `S390xArch`.
- Limited changes to ABI registration to minimize impact on existing functionality.

---

## Pull Request

**Status:** Ready for Pull Request

**PR Link:** TBD

**PR Description:** TBD

**Maintainer Feedback:**


---

## Learnings & Reflections

### Technical Skills Gained

- Understanding of Linux ABI conventions
- Understanding of syscall ABI implementations
- Architecture-specific debugging concepts
- Navigating a large open-source codebase
- Tracing architecture initialization and lookup logic

### Challenges Overcome

The main challenge was configuring the Pwndbg development environment on Windows.

The project's linting and testing workflows rely on Linux-oriented dependencies and build tools. During validation I installed uv, synchronized project dependencies, and investigated failures related to missing platform-specific components such as cmake and capstone.

Although automated validation could not be fully completed on Windows, I was able to verify the implementation through code review, git diff inspection, successful commits, and branch pushes.



### What I'd Do Differently Next Time

I would begin by tracing the architecture initialization flow earlier in the investigation process, which would have helped narrow the root cause more quickly.

---

## Resources Used

- https://github.com/pwndbg/pwndbg/issues/2889
- https://github.com/Gallopsled/pwntools/blob/dev/pwnlib/abi.py
- https://refspecs.linuxfoundation.org/ELF/zSeries/lzsabi0_s390/x414.html
- https://refspecs.linuxbase.org/ELF/zSeries/lzsabi0_s390.html
- Pwndbg source code
- Pwndbg contribution documentation
