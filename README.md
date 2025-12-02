# 🌺 MauiScript

**A modern DSL for .NET MAUI that ditches the angle brackets.**

```
// MauiScript
Stack.vertical
    Text "Welcome back"
        .style($Title)
    Entry
        .text(@Email)
        .placeholder("Email")
    Button "Sign In"
        .command(@LoginCommand)
        .style($PrimaryButton)
```

```xml
<!-- The XAML equivalent -->
<VerticalStackLayout>
    <Label Text="Welcome back" 
           Style="{StaticResource Title}" />
    <Entry Text="{Binding Email}" 
           Placeholder="Email" />
    <Button Text="Sign In" 
            Command="{Binding LoginCommand}"
            Style="{StaticResource PrimaryButton}" />
</VerticalStackLayout>
```

MauiScript is an opinionated, pragmatic DSL that:

- **Cuts the ceremony** — no `<`, `>`, `{Binding ...}`, or `StaticResource` boilerplate
- **Feels familiar** — borrows from SwiftUI, Jetpack Compose, React, and Vue
- **Targets full parity** — bindings, resources, layouts, animations, platform blocks
- **Transpiles to C#** — Roslyn source generator at build time, not runtime interpretation

> If we were designing a UI language for .NET MAUI *today*—for 2025/2026 developers across iOS, Android, and web—what would it look like?

MauiScript is the answer we're building.

---

## At a Glance

| | XAML | MauiScript |
|---|------|------------|
| **Binding** | `{Binding Email}` | `@Email` |
| **Two-way** | `{Binding Email, Mode=TwoWay}` | `@Email` (inferred) |
| **Resource** | `{StaticResource Primary}` | `$Primary` |
| **Command** | `Command="{Binding Login}"` | `.command(@Login)` |
| **Modifier** | `Margin="16" Padding="8"` | `.margin(16).padding(8)` |
| **Conditional** | `IsVisible="{Binding HasError}"` | `when @HasError` block |

---

## Documentation

### [Language Specification](docs/spec/MauiScript-Specification.md) — start here
<sub>[`docs/spec/MauiScript-Specification.md`](docs/spec/MauiScript-Specification.md)</sub>

The canonical reference. Covers syntax, semantics, design rationale, and a complete login page example demonstrating bindings, resources, styled text, and loading states. If you want to understand *what* MauiScript is, this is the document.

### [Development Plan](docs/DEVELOPMENT-PLAN.md)
<sub>[`docs/DEVELOPMENT-PLAN.md`](docs/DEVELOPMENT-PLAN.md)</sub>

Phased roadmap from "Todo App MVP" to full ecosystem. Covers repo structure, .NET 10 alignment, tooling (source generator, CLI, VS Code extension), and risk mitigation.

### [Roadmap Checklist](docs/ROADMAP-CHECKLIST.md)
<sub>[`docs/ROADMAP-CHECKLIST.md`](docs/ROADMAP-CHECKLIST.md)</sub>

Task-level tracker with complexity indicators. For contributors who want to know what to work on next.

---

## Status

MauiScript is currently in the **design and specification phase**. The language spec is largely complete; implementation has not yet begun.

We're looking for feedback on:
- Syntax and ergonomics — does this feel right?
- Transpilation strategy — Roslyn source generators vs. alternatives
- Tooling expectations — what would make you actually use this?

If you've shipped UI in SwiftUI, Compose, React, or XAML and have opinions, the spec was written with you in mind. Start there, then open an issue.
