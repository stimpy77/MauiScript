# MauiScript Development Plan

**Version**: 1.0  
**Last Updated**: December 2025  
**Target Platform**: .NET 10 / .NET MAUI 10 (LTS)

---

## Directory Structure

```
MauiScript/
├── README.md                           # Project overview, quick start
├── LICENSE                             # MIT or similar
├── .gitignore
├── Directory.Build.props               # Shared MSBuild properties
├── Directory.Packages.props            # Central package version management
├── global.json                         # Pin .NET SDK version (10.0.x)
├── MauiScript.sln                      # Root solution file
│
├── docs/
│   ├── spec/
│   │   └── MauiScript-Specification.md # Language specification
│   ├── DEVELOPMENT-PLAN.md             # This document
│   ├── ARCHITECTURE.md                 # System architecture (future)
│   └── tutorials/                      # Getting started guides (future)
│
├── docs-site/                          # Documentation website (VitePress)
│   ├── package.json
│   ├── .vitepress/
│   │   └── config.ts
│   └── src/
│       ├── index.md                    # Landing page
│       ├── getting-started/
│       ├── guide/
│       └── reference/
│
├── src/
│   ├── MauiScript.Core/                # Shared types, AST definitions
│   │   ├── MauiScript.Core.csproj
│   │   ├── Syntax/                     # AST nodes
│   │   ├── Semantics/                  # Binding resolution, type info
│   │   └── Diagnostics/                # Error/warning types
│   │
│   ├── MauiScript.Parser/              # Lexer + Parser (Sprache or custom)
│   │   ├── MauiScript.Parser.csproj
│   │   ├── Lexer.cs
│   │   ├── Parser.cs
│   │   └── Grammar/                    # Grammar definitions
│   │
│   ├── MauiScript.Compiler/            # Roslyn source generator
│   │   ├── MauiScript.Compiler.csproj
│   │   ├── SourceGenerator.cs          # IIncrementalGenerator entry
│   │   ├── CodeGen/                    # C# emission
│   │   │   ├── CSharpEmitter.cs
│   │   │   ├── BindingEmitter.cs
│   │   │   └── ResourceEmitter.cs
│   │   └── Analyzers/                  # Static analysis
│   │
│   ├── MauiScript.Runtime/             # Runtime helpers for generated code
│   │   ├── MauiScript.Runtime.csproj
│   │   ├── Converters/                 # Built-in converters (hasAny, isNotEmpty, etc.)
│   │   ├── Extensions/                 # .loading(), .animate() support
│   │   └── Controls/                   # Custom controls (SocialButton, StatCard, etc.)
│   │
│   ├── MauiScript.Tooling/             # CLI and migration tools
│   │   ├── MauiScript.Tooling.csproj
│   │   ├── Commands/
│   │   │   ├── MigrateCommand.cs       # XAML → MauiScript
│   │   │   ├── EjectCommand.cs         # MauiScript → C#
│   │   │   ├── ValidateCommand.cs
│   │   │   ├── FormatCommand.cs        # mauiscript fmt
│   │   │   └── DoctorCommand.cs        # Environment/workload check
│   │   └── Program.cs
│   │
│   └── MauiScript.Templates/           # dotnet new templates
│       ├── MauiScript.Templates.csproj
│       └── content/
│           ├── MauiScript.App/         # dotnet new mauiscript-app
│           │   ├── template.json
│           │   ├── ProjectName.csproj
│           │   ├── MauiProgram.cs
│           │   ├── App.maui
│           │   ├── MainPage.maui
│           │   ├── MainPageViewModel.cs
│           │   └── Resources/
│           │       └── theme.maui
│           ├── MauiScript.Page/        # dotnet new mauiscript-page
│           │   ├── template.json
│           │   ├── PageName.maui
│           │   └── PageNameViewModel.cs
│           └── MauiScript.Component/   # dotnet new mauiscript-component
│               ├── template.json
│               └── ComponentName.maui
│
├── tools/
│   ├── vscode-mauiscript/              # VS Code extension
│   │   ├── package.json
│   │   ├── syntaxes/
│   │   │   └── mauiscript.tmLanguage.json
│   │   ├── language-configuration.json
│   │   └── src/
│   │       ├── extension.ts
│   │       └── languageServer/         # LSP implementation (future)
│   │
│   └── MauiScript.Playground/          # Interactive syntax explorer (Phase 3+)
│       ├── MauiScript.Playground.csproj
│       └── ...                         # MAUI app with live preview
│
├── samples/
│   ├── TodoApp/                        # Milestone 1 demo
│   │   ├── TodoApp.sln
│   │   ├── TodoApp/                    # MAUI project
│   │   │   ├── TodoApp.csproj
│   │   │   ├── Views/
│   │   │   │   ├── MainPage.maui
│   │   │   │   ├── TodoDetailPage.maui
│   │   │   │   └── SettingsPage.maui
│   │   │   ├── ViewModels/
│   │   │   ├── Models/
│   │   │   └── Resources/
│   │   │       └── theme.maui
│   │   └── TodoApp.Tests/
│   │
│   ├── SettingsPage/                   # Standalone settings demo
│   ├── LoginFlow/                      # Auth flow demo
│   ├── ProductBrowser/                 # E-commerce demo
│   ├── ChatApp/                        # Messaging UI demo
│   └── Dashboard/                      # Charts + analytics demo
│
├── tests/
│   ├── MauiScript.Parser.Tests/
│   │   ├── LexerTests.cs
│   │   ├── ParserTests.cs
│   │   └── Snapshots/                  # Snapshot testing for AST
│   │
│   ├── MauiScript.Compiler.Tests/
│   │   ├── CodeGenTests.cs
│   │   ├── BindingInferenceTests.cs
│   │   └── Snapshots/                  # Snapshot testing for C# output
│   │
│   └── MauiScript.Integration.Tests/
│       └── EndToEndTests.cs
│
└── benchmarks/
    └── MauiScript.Benchmarks/
        ├── ParserBenchmarks.cs
        └── CodeGenBenchmarks.cs
```

---

## Technology Stack & Prerequisites

### .NET 10 SDK Leveraged Features

| Feature | How We Use It |
|---------|---------------|
| **LTS (3-year support)** | Stable foundation for production tooling |
| **Roslyn Source Generators** | MauiScript → C# compilation at build time |
| **Incremental Generators** | Fast rebuild; only regenerate changed `.maui` files |
| **C# 14** | File-scoped types, collection expressions in generated code |
| **SDK `dotnet tool exec`** | One-shot CLI invocation for migration/validation |
| **Native AOT improvements** | Future: Pre-compile parser for faster cold starts |

### .NET MAUI 10 Alignment

| MAUI 10 Feature | MauiScript Support |
|-----------------|-------------------|
| **XAML Source Generator** | We generate C# directly (bypasses XAML entirely) |
| **Async animation methods** | `.animate()` emits `FadeToAsync`, `ScaleToAsync`, etc. |
| **SafeAreaEdges enum** | `.safeArea(none\|softInput\|container\|all)` |
| **CollectionView as default** | `Collection()` maps to improved CollectionView |
| **Nullable DatePicker/TimePicker** | Automatic handling in binding inference |
| **Switch.OffColor** | `.offColor()` modifier |
| **RefreshView.IsRefreshEnabled** | `.refreshEnabled()` modifier |
| **MessagingCenter removal** | No generated MessagingCenter code; recommend CommunityToolkit.Mvvm |
| **Deprecated controls removed** | ListView, TableView, *Cell types not in grammar |

### Central Package Management

All package versions are centralized in `Directory.Packages.props` to ensure consistency:

```xml
<!-- Directory.Packages.props -->
<Project>
  <PropertyGroup>
    <ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>
  </PropertyGroup>
  <ItemGroup>
    <!-- Roslyn / Source Generators -->
    <PackageVersion Include="Microsoft.CodeAnalysis.CSharp" Version="4.12.0" />
    <PackageVersion Include="Microsoft.CodeAnalysis.Analyzers" Version="3.3.4" />
    
    <!-- Parser -->
    <PackageVersion Include="Sprache" Version="2.4.0" />
    
    <!-- MAUI -->
    <PackageVersion Include="Microsoft.Maui.Controls" Version="10.0.0" />
    <PackageVersion Include="Microsoft.Maui.Controls.Compatibility" Version="10.0.0" />
    
    <!-- Community Toolkit -->
    <PackageVersion Include="CommunityToolkit.Mvvm" Version="8.4.0" />
    <PackageVersion Include="CommunityToolkit.Maui" Version="10.0.0" />
    <PackageVersion Include="CommunityToolkit.Maui.Markup" Version="5.0.0" />
    
    <!-- Testing -->
    <PackageVersion Include="Microsoft.NET.Test.Sdk" Version="17.12.0" />
    <PackageVersion Include="xunit" Version="2.9.2" />
    <PackageVersion Include="xunit.runner.visualstudio" Version="2.8.2" />
    <PackageVersion Include="Verify.Xunit" Version="28.0.0" />  <!-- Snapshot testing -->
    <PackageVersion Include="FluentAssertions" Version="7.0.0" />
    
    <!-- Benchmarking -->
    <PackageVersion Include="BenchmarkDotNet" Version="0.14.0" />
    
    <!-- CLI -->
    <PackageVersion Include="System.CommandLine" Version="2.0.0-beta4.24324.3" />
  </ItemGroup>
</Project>
```

> **Note**: Versions above are illustrative based on .NET 10 RTM timeframe. Pin to actual RTM versions when available.

### Published NuGet Packages

| Package | Description |
|---------|-------------|
| `MauiScript` | Meta-package referencing Compiler + Runtime |
| `MauiScript.Compiler` | Roslyn source generator + analyzers |
| `MauiScript.Runtime` | Converters, extensions, helper controls |
| `MauiScript.Templates` | `dotnet new` project/item templates |
| `MauiScript.Tools` | Global CLI tool (`mauiscript` command) |

---

## Phase 1: Todo App MVP (Milestone 1 — Announcement-Ready)

**Duration**: 8–10 weeks  
**Goal**: Ship a working Todo App built entirely with MauiScript, demonstrating the core value proposition.

> **Sequencing guidance**: Build Compiler + Runtime first (Weeks 1–6), then sprint on Templates + Docs (Weeks 7–10) once the syntax is stable. Don’t let docs perfection block compiler progress.

### 1.1 Foundation (Weeks 1–2)

| Task | Deliverable |
|------|-------------|
| Project scaffolding | Solution structure per directory layout above |
| `global.json` | Pin to .NET 10.0.100 SDK |
| `Directory.Build.props` | Shared settings (LangVersion=14, Nullable, ImplicitUsings) |
| `Directory.Packages.props` | Central package version management |
| CI/CD setup (GitHub Actions) | Build + test on push; pack on tag |
| Basic README | "What is MauiScript" + vision statement |

### 1.2 Parser MVP (Weeks 2–4)

| Task | Deliverable |
|------|-------------|
| **Lexer** | Tokenize: identifiers, literals, `@`, `$`, `.`, `>`, `#`, indentation |
| **Parser** | Parse basic elements: `Text`, `Button`, `Entry`, `Stack`, `Image` |
| **AST types** | `ElementNode`, `ModifierNode`, `BindingNode`, `ResourceRefNode` |
| **Indentation handling** | Track indent levels for hierarchy |
| **Unit tests** | 80%+ coverage on parser with snapshot tests |

**Grammar Subset for Phase 1**:
```
element     = identifier content? modifiers? children?
content     = string_literal | binding
binding     = '@' identifier ('|' converter)*
resource    = '$' identifier
modifier    = '.' identifier arguments?
children    = INDENT (element | when_block)* DEDENT
when_block  = 'when' binding NEWLINE INDENT children DEDENT
```

### 1.3 Code Generator MVP (Weeks 4–6)

| Task | Deliverable |
|------|-------------|
| **IIncrementalGenerator** | Entry point, file discovery |
| **CSharpEmitter** | Emit `VerticalStackLayout`, `Label`, `Button`, `Entry`, `Image` |
| **Binding emission** | OneWay/TwoWay based on control type (per Appendix A) |
| **Resource emission** | `DynamicResource()` calls |
| **Page scaffolding** | Generate `partial class` with constructor |
| **Source mapping** | `#line` pragmas pointing to `.maui` file |
| **Integration tests** | Compile generated C# → verify MAUI app runs |

**Target Generated Code Quality**:
```csharp
// Generated: MainPage.g.cs
#nullable enable
#line 1 "Views/MainPage.maui"

public partial class MainPage : ContentPage
{
    public MainPage(MainPageViewModel viewModel)
    {
        BindingContext = viewModel;
        Content = new VerticalStackLayout { /* ... */ };
    }
}
```

### 1.4 Runtime Helpers (Week 6)

| Task | Deliverable |
|------|-------------|
| **Built-in converters** | `hasAny`, `isNotEmpty`, `not`, `format`, `alpha` |
| **LoadingStateExtension** | `.WithLoadingState()` for Button |
| **AnimationExtensions** | Wrapper for async animation methods |
| **Fluent extensions** | If CommunityToolkit.Maui.Markup gaps exist |

### 1.5 VS Code Extension v0.1 (Week 7)

| Task | Deliverable |
|------|-------------|
| **TextMate grammar** | Syntax highlighting for `.maui` files |
| **Language configuration** | Bracket matching, auto-indent rules |
| **Snippet support** | `page`, `stack`, `foreach`, `when` snippets |
| **File icons** | `.maui` file icon |

### 1.6 Todo App Sample (Weeks 7–9)

| Feature | MauiScript Syntax Exercised |
|---------|------------------------------|
| **Task list** | `Collection`, `foreach`, `template` |
| **Add task** | `Entry`, `Button`, `@` binding, `.command()` |
| **Toggle complete** | `CheckBox`, inverted binding `@!` |
| **Delete task** | Swipe action or tap with command param |
| **Empty state** | `empty` block in Collection |
| **Pull-to-refresh** | `RefreshView`, `.loading()` |
| **Filter (All/Active/Done)** | `Chip` selection, converter |
| **Persist locally** | ViewModel with SQLite (not MauiScript concern) |
| **Theme** | `theme.maui` resource file, `$` references |
| **Dark mode** | `@dark` resource overrides |

### 1.7 Templates (Week 8)

| Task | Deliverable |
|------|-------------|
| **`mauiscript-app` template** | Full MAUI project with MauiScript pre-wired |
| **`mauiscript-page` template** | Page + ViewModel pair |
| **`mauiscript-component` template** | Reusable component scaffold |
| **Template testing** | Verify `dotnet new` works across platforms |
| **NuGet packaging** | `MauiScript.Templates` ready for publish |

**Template Usage (Target UX)**:
```bash
# Create new MauiScript app
dotnet new install MauiScript.Templates
dotnet new mauiscript-app -n MyTodoApp
cd MyTodoApp
dotnet build

# Add a new page
dotnet new mauiscript-page -n Settings --namespace MyTodoApp.Views
```

### 1.8 Documentation & Announcement (Weeks 9–10)

| Deliverable | Contents |
|-------------|----------|
| **README.md** | Quick start using templates, installation, basic example |
| **Docs site (VitePress)** | Landing page, Getting Started, Language Tour, Todo Walkthrough |
| **Blog post draft** | "Introducing MauiScript" |
| **Comparison screenshots** | XAML vs MauiScript side-by-side |
| **Video demo** | 2-minute screencast of Todo App |
| **GitHub release v0.1.0** | Tagged, with release notes |

**Docs Site Structure**:
```
docs-site/src/
├── index.md              # Hero + value prop
├── getting-started/
│   ├── installation.md   # dotnet new install + templates
│   ├── hello-world.md    # First .maui file
│   └── todo-tutorial.md  # Step-by-step Todo App
├── guide/
│   ├── syntax.md         # Core syntax overview
│   ├── bindings.md       # @ prefix, converters
│   ├── resources.md      # $ prefix, themes
│   └── layouts.md        # Stack, Grid, Flex
└── reference/
    ├── controls.md       # All supported controls
    ├── modifiers.md      # All modifiers
    └── cli.md            # mauiscript commands
```

### Milestone 1 Exit Criteria

- [ ] `dotnet new mauiscript-app` creates working project
- [ ] Todo App compiles and runs on iOS + Android simulators
- [ ] Todo App is dogfooded as the primary manual test bed for MauiScript development
- [ ] All core syntax from spec Phase 1 is implemented
- [ ] VS Code extension published to marketplace (preview)
- [ ] 80%+ test coverage on Parser + Compiler
- [ ] Docs site live with Getting Started + Todo tutorial
- [ ] NuGet packages published (preview): Compiler, Runtime, Templates
- [ ] No known blocking bugs

---

## Phase 2: Full Control Library + Settings Sample

**Duration**: 6–8 weeks  
**Goal**: Support all MAUI controls; ship Settings Page sample demonstrating platform-specific blocks.

### 2.1 Extended Parser

| Addition | Notes |
|----------|-------|
| **Grid layout** | `Grid[2, 3]`, `Grid["auto, *", "*, *"]` |
| **Flex layout** | `Flex.row.wrap` |
| **Platform blocks** | `.iOS { }`, `.android { }`, `.windows { }` |
| **Inline block syntax** | `Button "X" { .command(@Y), .style($Z) }` |
| **Span syntax** | `Text [ span "..." { weight: bold } ]` |
| **Ref modifier** | `.ref(fieldName)` |
| **Named params** | `.padding(horizontal: 24, vertical: 16)` |

### 2.2 Extended Code Generator

| Addition | Notes |
|----------|-------|
| **All layout types** | Grid, FlexLayout, AbsoluteLayout |
| **All input controls** | Editor, Picker, DatePicker, TimePicker, Slider, Stepper, Switch |
| **All display controls** | Image, ImageButton, ActivityIndicator, ProgressBar |
| **Platform conditionals** | `#if IOS` / `OnPlatform<>` patterns |
| **Span/FormattedString** | Rich text emission |

### 2.3 Settings Page Sample

| Feature | Syntax Exercised |
|---------|------------------|
| **Grouped sections** | Nested Stack/Card hierarchy |
| **Toggle switches** | `Switch`, `.toggled(@)` |
| **Sliders** | `Slider`, `.value(@)` |
| **Pickers** | `Picker`, `.items(@)`, `.selectedIndex(@)` |
| **Platform blocks** | iOS-specific appearance, Android material design |
| **Nested conditionals** | `when @IsPremium` → premium-only settings |

### 2.4 Login/Signup Sample

| Feature | Syntax Exercised |
|---------|------------------|
| **Rich text** | Legal disclaimer with tap targets |
| **Validation errors** | Conditional visibility + animations |
| **Secure entry** | `.secure(@!ShowPassword)` |
| **Social buttons** | Platform-specific visibility |
| **Loading state** | `.loading(@IsLoading)` |

---

## Phase 3: Tooling Excellence

**Duration**: 8–10 weeks  
**Goal**: Full Language Server Protocol (LSP) implementation; complete CLI toolchain; interactive playground.

### 3.1 Language Server

| Feature | Benefit |
|---------|--------|
| **Completions** | After `@` → ViewModel properties; after `$` → resources |
| **Hover** | Documentation for controls and modifiers |
| **Go to Definition** | Jump to ViewModel, resource file |
| **Find References** | Where is `@Email` used? |
| **Diagnostics** | Real-time error squiggles |
| **Code Actions** | "Create property in ViewModel", "Extract style" |
| **Rename Symbol** | Rename binding across `.maui` and `.cs` files |
| **Document Symbols** | Outline view of page structure |

**Implementation Stack**:
- `OmniSharp.Extensions.LanguageServer` NuGet
- Embed parser in LSP server
- Workspace-aware (knows about ViewModels via Roslyn compilation)
- Integrates with both VS Code and Visual Studio

**LSP "Done" Criteria**: Completions, hover, diagnostics, and go-to-definition are stable when tested against the Todo, Settings, and Login samples. Avoid endless polishing—ship when core workflows are solid.

### 3.2 CLI Tools

```bash
# Install globally
dotnet tool install -g MauiScript.Tools

# Check environment (workloads, SDK, package compatibility)
mauiscript doctor

# Format .maui files (opinionated, like dotnet format)
mauiscript fmt ./Views/
mauiscript fmt ./Views/MainPage.maui --check  # CI mode, no write

# Migrate XAML to MauiScript
mauiscript migrate ./Views/*.xaml --output ./Views/
mauiscript migrate ./Views/LoginPage.xaml --dry-run  # Preview changes

# Validate syntax without building
mauiscript validate ./Views/

# Eject to pure C# (escape hatch)
mauiscript eject ./Views/MainPage.maui --output ./Views/MainPage.cs

# Watch mode (recompile on save, useful for CI/dev)
mauiscript watch ./Views/
```

**`mauiscript doctor` Output Example**:
```
✔ .NET SDK 10.0.100
✔ MAUI workload installed (10.0.0)
✔ MauiScript.Compiler 0.2.0
✔ MauiScript.Runtime 0.2.0 (compatible)
⚠ MauiScript.Templates 0.1.0 → 0.2.0 available
  Run: dotnet new install MauiScript.Templates::0.2.0
```

**`mauiscript fmt` Behavior**:
- Consistent indentation (2 or 4 spaces, configurable)
- Modifier ordering (alphabetical or semantic)
- Trailing newline normalization
- Respects `.editorconfig` where applicable

### 3.3 Interactive Playground

A standalone MAUI app for experimenting with MauiScript syntax:

| Feature | Description |
|---------|-------------|
| **Live editor** | Edit `.maui` code in left pane |
| **Generated C# view** | See transpiled output in right pane |
| **Live preview** | Render actual MAUI controls (hot reload) |
| **Error highlighting** | Inline diagnostics from parser |
| **Snippet library** | Pre-built examples to explore syntax |

Location: `tools/MauiScript.Playground/`

### 3.4 Error Messages & Developer Experience

| Scenario | Error Message |
|----------|---------------|
| Unknown control | `Error MS1001: Unknown element 'Buttton'. Did you mean 'Button'?` |
| Missing ViewModel property | `Warning MS2001: Property 'Emial' not found on LoginViewModel. Did you mean 'Email'?` |
| Invalid binding mode | `Error MS1002: Entry.IsEnabled does not support TwoWay binding.` |
| Indentation mismatch | `Error MS1003: Unexpected indent level at line 42. Expected 4 spaces, found 6.` |
| Deprecated control | `Warning MS2010: 'ListView' is deprecated in MAUI 10. Use 'Collection' instead.` |
| Missing resource | `Error MS1004: Resource 'PrmaryButton' not found. Did you mean 'PrimaryButton'?` |

---

## Phase 4: Visual Studio Extension

**Duration**: 6–8 weeks  
**Goal**: First-class Visual Studio 2022+ support.

### 4.1 Extension Features

| Feature | Notes |
|---------|-------|
| **Syntax highlighting** | Classifier based on parser |
| **IntelliSense** | Leverage LSP (shared with VS Code) |
| **Error List integration** | Diagnostics in IDE |
| **Solution Explorer** | `.maui` files with custom icon |
| **Build integration** | MSBuild props/targets |

### 4.2 Project & Item Templates

| Template | Description |
|----------|-------------|
| **MauiScript App** | "Add New Project" option in VS |
| **MauiScript Page** | "Add → New Item → MauiScript Page" |
| **MauiScript Component** | "Add → New Item → MauiScript Component" |

These reuse the content from `MauiScript.Templates` but are surfaced natively in Visual Studio's template dialogs.

### 4.3 Stretch Goals

| Feature | Notes |
|---------|-------|
| **Hot Reload indicator** | Show when regeneration happens |
| **Side-by-side preview** | Generated C# in read-only pane |
| **Design-time preview** | Render MAUI controls in preview window (very hard) |
| **Refactoring support** | Extract component, inline style |

---

## Phase 5: Ecosystem & Advanced Samples

**Duration**: Ongoing  
**Goal**: Community growth; demonstrate complex real-world apps.

### 5.1 Product Browser Sample

| Feature | Syntax Exercised |
|---------|------------------|
| **Virtualized grid** | `Collection` with grid layout |
| **Search + filters** | SearchBar, filter chips |
| **Master-detail nav** | Shell navigation |
| **Image galleries** | CarouselView integration |
| **Pull-to-refresh** | RefreshView with loading overlay |

### 5.2 Chat/Messaging Sample

| Feature | Syntax Exercised |
|---------|------------------|
| **Variable-height cells** | Complex Collection templates |
| **Grouped by date** | Section headers |
| **Rich bubbles** | Formatted text, images |
| **Typing indicators** | Animated visibility |
| **Scroll-to-bottom** | Element references, `#messageList` |

### 5.3 Dashboard Sample

| Feature | Syntax Exercised |
|---------|------------------|
| **Third-party charts** | `use Charts from "Microcharts.Maui"` |
| **Stat cards** | Custom component composition |
| **Real-time updates** | Binding performance |
| **Quick actions** | Grid of action buttons |

### 5.4 Dashboard + .NET Aspire Variant (Optional)

Demonstrate MauiScript in a cloud-native .NET 10 ecosystem:

| Feature | Description |
|---------|-------------|
| **Aspire service defaults** | `builder.AddServiceDefaults()` integration |
| **Service discovery** | Consume backend APIs via Aspire |
| **OpenTelemetry** | Distributed tracing from mobile to backend |
| **Multi-project solution** | MAUI client + Aspire host + API |

This is a stretch goal showcasing .NET 10's full-stack story.

### 5.5 Community & Ecosystem

| Initiative | Notes |
|------------|-------|
| **Component library** | `MauiScript.Components` NuGet (SocialButton, StatCard, etc.) |
| **Third-party control support** | Guidance for Syncfusion, Telerik, DevExpress |
| **Discord/GitHub Discussions** | Community support |
| **Contribution guide** | CONTRIBUTING.md |
| **Awesome MauiScript** | Curated list of community resources |

---

## Support Utilities & Infrastructure

### Build-Time Requirements

| Tool | Version | Purpose |
|------|---------|---------|
| .NET SDK | 10.0.100+ | Build, test, pack |
| Node.js | 20 LTS | VS Code extension build |
| VS Code | 1.85+ | Extension development |

### CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  release:
    types: [published]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'
      - run: dotnet restore
      - run: dotnet build --no-restore
      - run: dotnet test --no-build --collect:"XPlat Code Coverage"
      
  pack:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'
      - run: dotnet pack -c Release -o ./artifacts
      - uses: actions/upload-artifact@v4
        with:
          name: nuget-packages
          path: ./artifacts/*.nupkg

  publish-nuget:
    if: github.event_name == 'release'
    needs: pack
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: nuget-packages
          path: ./artifacts
      - run: dotnet nuget push ./artifacts/*.nupkg --api-key ${{ secrets.NUGET_API_KEY }} --source https://api.nuget.org/v3/index.json

  vscode-extension:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: tools/vscode-mauiscript
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run package
      - uses: actions/upload-artifact@v4
        with:
          name: vscode-extension
          path: tools/vscode-mauiscript/*.vsix

  docs:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: docs-site
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run build
      # Deploy to GitHub Pages on main branch push
```

### Published Artifacts

| Artifact | Destination | Trigger |
|----------|-------------|--------|
| `MauiScript.*.nupkg` | NuGet.org | GitHub Release |
| `mauiscript-*.vsix` | VS Marketplace | GitHub Release |
| Docs site | GitHub Pages | Push to main |

### Versioning Strategy

| Component | Versioning |
|-----------|-----------|
| MauiScript.* packages | SemVer, synchronized across all packages |
| VS Code Extension | Marketplace versioning, follows package version |
| Specification | `v0.x.y` during preview, `v1.0` at stable |

---

## Risk Mitigation

| Risk | Mitigation |
|------|------------|
| **Parser complexity** | Start with Sprache; move to hand-rolled if perf issues |
| **MAUI API churn** | Target stable .NET 10 LTS; avoid preview features |
| **Source generator debugging** | Use `launch.json` with `debugger` attach; verbose logging |
| **Hot reload breaks** | Validate with each MAUI servicing update |
| **Community adoption** | Focus on excellent docs + comparison guides |
| **Template drift** | Templates tested in CI; version-lock to MauiScript packages |
| **MAUI workload issues** | `mauiscript doctor` catches common setup problems early |
| **Breaking syntax changes** | Spec versioning + migration tooling (`mauiscript migrate --version`) |

---

## Timeline Summary

| Phase | Duration | Key Deliverable |
|-------|----------|-----------------|
| **Phase 1** | 8–10 weeks | Todo App MVP + Templates + Docs Site + Announcement |
| **Phase 2** | 6–8 weeks | Full controls + Settings/Login samples |
| **Phase 3** | 8–10 weeks | LSP + CLI tools (fmt, doctor) + Playground |
| **Phase 4** | 6–8 weeks | Visual Studio extension + VS templates |
| **Phase 5** | Ongoing | Advanced samples + Aspire demo + ecosystem |

**Total to "Production Ready"**: ~30–36 weeks (7–9 months)

---

## Next Steps (Immediate)

1. **Initialize solution structure** per directory layout
2. **Set up `global.json`** with .NET 10.0.100
3. **Set up `Directory.Build.props`** and `Directory.Packages.props`
4. **Create `MauiScript.Core`** project with AST types
5. **Write first lexer tests** for basic tokenization
6. **Create VS Code extension scaffold** with TextMate grammar
7. **Scaffold `MauiScript.Templates`** with basic app template
8. **Set up GitHub Actions** CI workflow

---

*This is a living document. Update as implementation progresses.*
