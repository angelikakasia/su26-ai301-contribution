# Contribution 1: Add ABI/SyscallABI Support for LoongArch64 + s390x

**Contribution Number:** 1  
**Student:** Angelika Szymanowska  
**Issue:** https://github.com/pwndbg/pwndbg/issues/2889  
**Status:** Phase I - Complete

---

## Why I Chose This Issue

I chose this issue because it combines Python development with low-level operating system concepts such as system calls and application binary interfaces (ABIs). As a cybersecurity student interested in reverse engineering, debugging, and system internals, I wanted to work on a contribution that would expose me to architecture-specific behavior in Linux systems.

This issue also provides an opportunity to learn how Pwndbg and Pwntools represent syscall conventions across different processor architectures. Through this contribution, I hope to strengthen my Python skills, improve my understanding of Linux system calls, and gain practical experience contributing to an open-source cybersecurity project.

---

## Understanding the Issue

### Problem Description

Pwndbg currently supports ABI and syscall handling for several architectures including x86-64, ARM, AArch64, MIPS, PowerPC, and RISC-V. However, support for LoongArch64 and s390x is missing.

### Expected Behavior

Pwndbg should correctly recognize LoongArch64 and s390x architectures, identify their ABI definitions, and display syscall names and arguments correctly during debugging.

### Current Behavior

LoongArch64 and s390x are not included in the ABI and syscall ABI mappings, preventing proper syscall decoding and architecture-specific handling.

### Affected Components

- `pwndbg/lib/abi.py`
- `pwndbg/aglib/disasm/arch.py`
- Related ABI and syscall handling logic

---

## Reproduction Process

### Environment Setup

Phase II - Not Started

### Steps to Reproduce

1. Clone the Pwndbg repository.
2. Open `pwndbg/lib/abi.py`.
3. Locate `DEFAULT_ABIS` and `SYSCALL_ABIS`.
4. Observe that LoongArch64 and s390x are not present.
5. Open `pwndbg/aglib/disasm/arch.py`.
6. Observe that syscall name mappings for LoongArch64 and s390x are missing.

### Reproduction Evidence

- **Commit showing reproduction:** TBD
- **Screenshots/logs:** TBD
- **My findings:** Initial review indicates that ABI mappings and syscall architecture mappings for LoongArch64 and s390x have not been implemented.

---

## Solution Approach

### Analysis

The root cause appears to be missing architecture definitions in the ABI and syscall ABI mappings. Additional architecture-to-syscall-name mappings are also required.

### Proposed Solution

Add LoongArch64 and s390x ABI definitions to the existing architecture mappings and update syscall architecture resolution to support both architectures.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Pwndbg lacks ABI and syscall support for LoongArch64 and s390x.

**Match:** Existing implementations for AArch64, RISC-V, MIPS, and PowerPC provide examples of how architecture-specific ABIs are defined.

**Plan:**

1. Review existing ABI definitions in `pwndbg/lib/abi.py`.
2. Review equivalent definitions in Pwntools.
3. Research LoongArch64 and s390x syscall conventions.
4. Add ABI entries to `DEFAULT_ABIS`.
5. Add syscall ABI entries to `SYSCALL_ABIS`.
6. Update architecture mappings in `pwndbg/aglib/disasm/arch.py`.
7. Verify compatibility with Pwndbg's modified `SyscallABI` implementation.
8. Run project tests and validation checks.

**Implement:** TBD

**Review:**

- [ ] Follow project contribution guidelines
- [ ] Follow project coding style
- [ ] Ensure no existing architecture support is affected
- [ ] Verify compatibility with SyscallABI implementation

**Evaluate:**

Confirm that LoongArch64 and s390x architectures are recognized and that syscall decoding behaves correctly.

---

## Testing Strategy

### Unit Tests

- [ ] Verify LoongArch64 ABI mapping
- [ ] Verify s390x ABI mapping
- [ ] Verify syscall ABI registration

### Integration Tests

- [ ] Validate architecture detection
- [ ] Validate syscall name resolution

### Manual Testing

To be completed during implementation.

---

## Implementation Notes

### Week 1 Progress

- Selected issue.
- Reviewed issue description and maintainer comments.
- Identified relevant source files.
- Began researching ABI and syscall handling in Pwndbg and Pwntools.

### Week 2 Progress

TBD

### Code Changes

- **Files modified:** TBD
- **Key commits:** TBD
- **Approach decisions:** TBD

---

## Pull Request

**PR Link:** TBD

**PR Description:** TBD

**Maintainer Feedback:**

- TBD

**Status:** Not Submitted

---

## Learnings & Reflections

### Technical Skills Gained

TBD

### Challenges Overcome

TBD

### What I'd Do Differently Next Time

TBD

---

## Resources Used

- https://github.com/pwndbg/pwndbg/issues/2889
- https://github.com/Gallopsled/pwntools/blob/dev/pwnlib/abi.py
- Pwndbg source code documentation
