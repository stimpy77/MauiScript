# MauiScript

A modern Domain-Specific Language (DSL) for building .NET MAUI UI as an alternative to XAML.

MauiScript is an opinionated, pragmatic DSL that:
- Dramatically reduces the verbosity and angle-bracket ceremony of XAML
- Feels familiar to developers from SwiftUI, Jetpack Compose, React, and Vue
- Targets full .NET MAUI feature parity (bindings, resources, layouts, animations)
- Is designed to be transpiled to C# at build time with strong tooling support

The initiative started from a simple question:
> If we were designing a UI language for .NET MAUI *today*—for 2025/2026 developers across web, iOS, and Android—what would it look like, and why would they choose it over XAML?

MauiScript is the answer we are exploring.

---

## Key Documents

### 1. MauiScript Language Specification (Start Here)

**File:** `docs/spec/MauiScript-Specification.md`

This is the *canonical* document for MauiScript. It:
- Defines the syntax and semantics of the DSL
- Explains design decisions, trade-offs, and influences (SwiftUI, Compose, React, CSS)
- Walks through a full real-world example (e.g., a login page) with:
  - Layout composition
  - Data bindings (one-way, two-way, commands)
  - Resource references
  - Styled/interactive text
  - Loading and error states
- Describes how MauiScript can be transpiled to C# at build time

If you want to understand what MauiScript is and why it exists, **read the spec first**.

---

### 2. Development Plan

**File:** `docs/DEVELOPMENT-PLAN.md`

A high-level, phased roadmap for taking MauiScript from idea to an announcement-ready Todo app and beyond. It covers:
- Repository and solution structure
- Phased milestones (core language, samples, tooling, ecosystem)
- .NET 10 / .NET MAUI 10 alignment
- Tooling (source generator, CLI, VS Code/VS extensions)
- Risks and mitigation strategies

Use this if you want to understand *how* we plan to build MauiScript.

---

### 3. Roadmap Checklist

**File:** `docs/ROADMAP-CHECKLIST.md`

A task-level checklist derived from the development plan. It provides:
- Concrete `[ ]` items for each phase and milestone
- Superscript complexity indicators (¹–⁴) for each task
- A quick way to track implementation progress against the plan

Use this if you’re working on MauiScript and want to know **what to do next**.

---

## Contributing & Feedback

Right now, MauiScript is an experiment. Feedback is welcome on:
- The DSL design itself (syntax, readability, ergonomics)
- The feasibility of the transpilation strategy
- Tooling expectations for a modern MAUI UI language

If you’re coming from SwiftUI, Compose, React, or XAML and have strong opinions about UI languages, the spec is written with you in mind—please start there and file issues or notes based on your experience.
