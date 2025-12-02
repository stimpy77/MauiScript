# MauiScript Roadmap Checklist

**Version**: 1.0  
**Last Updated**: December 2025  
**Companion to**: [DEVELOPMENT-PLAN.md](./DEVELOPMENT-PLAN.md)

## Usage Notes

- **[ ]** = Not started
- **[~]** = In progress
- **[X]** = Complete
- **[!]** = Blocked/Issue
- **[-]** = Not applicable/Skipped

**Complexity Indicators** (superscript after checkbox):
- ¹ = Simple (docs, scaffolding, config files)
- ² = Medium (templates, CLI tools, basic codegen)
- ³ = Complex (parser, source generator, LSP)
- ⁴ = Critical path/high risk (core compiler work)

---

## Phase 1: Todo App MVP (Milestone 1 — Announcement-Ready)
**Duration**: 8–10 weeks  
**Goal**: Ship a working Todo App built entirely with MauiScript

### 1.1 Foundation (Weeks 1–2)

- [ ]¹ Project scaffolding (solution structure per directory layout)
- [ ]¹ `global.json` pinned to .NET 10.0.100 SDK
- [ ]¹ `Directory.Build.props` with shared settings (LangVersion=14, Nullable, ImplicitUsings)
- [ ]¹ `Directory.Packages.props` for central package version management
- [ ]² CI/CD setup (GitHub Actions) - build + test on push; pack on tag
- [ ]¹ Basic README with "What is MauiScript" + vision statement

### 1.2 Parser MVP (Weeks 2–4)

- [ ]⁴ **Lexer** tokenizes: identifiers, literals, `@`, `$`, `.`, `>`, `#`, indentation
- [ ]⁴ **Parser** parses basic elements: `Text`, `Button`, `Entry`, `Stack`, `Image`
- [ ]³ AST types defined: `ElementNode`, `ModifierNode`, `BindingNode`, `ResourceRefNode`
- [ ]⁴ Indentation handling tracks indent levels for hierarchy
- [ ]³ Unit tests achieve 80%+ coverage on parser with snapshot tests

### 1.3 Code Generator MVP (Weeks 4–6)

- [ ]⁴ **IIncrementalGenerator** entry point with file discovery
- [ ]³ **CSharpEmitter** generates: `VerticalStackLayout`, `Label`, `Button`, `Entry`, `Image`
- [ ]³ Binding emission supports OneWay/TwoWay based on control type
- [ ]³ Resource emission generates `DynamicResource()` calls
- [ ]² Page scaffolding generates `partial class` with constructor
- [ ]³ Source mapping includes `#line` pragmas pointing to `.maui` file
- [ ]³ Integration tests compile generated C# → verify MAUI app runs

### 1.4 Runtime Helpers (Week 6)

- [ ]² Built-in converters: `hasAny`, `isNotEmpty`, `not`, `format`, `alpha`
- [ ]² LoadingStateExtension `.WithLoadingState()` for Button
- [ ]² AnimationExtensions wrapper for async animation methods
- [ ]² Fluent extensions for any CommunityToolkit.Maui.Markup gaps

### 1.5 VS Code Extension v0.1 (Week 7)

- [ ]² TextMate grammar for `.maui` syntax highlighting
- [ ]² Language configuration with bracket matching and auto-indent rules
- [ ]² Snippet support: `page`, `stack`, `foreach`, `when`
- [ ]¹ File icons for `.maui` files

### 1.6 Todo App Sample (Weeks 7–9)

- [ ]² Task list: `Collection`, `foreach`, `template`
- [ ]² Add task: `Entry`, `Button`, `@` binding, `.command()`
- [ ]² Toggle complete: `CheckBox`, inverted binding `@!`
- [ ]² Delete task: Swipe action or tap with command param
- [ ]² Empty state: `empty` block in Collection
- [ ]² Pull-to-refresh: `RefreshView`, `.loading()`
- [ ]² Filter (All/Active/Done): `Chip` selection, converter
- [ ]¹ Persist locally: ViewModel with SQLite (not MauiScript concern)
- [ ]² Theme: `theme.maui` resource file, `$` references
- [ ]² Dark mode: `@dark` resource overrides

### 1.7 Templates (Week 8)

- [ ]² **`mauiscript-app` template**: Full MAUI project with MauiScript pre-wired
- [ ]² **`mauiscript-page` template**: Page + ViewModel pair
- [ ]² **`mauiscript-component` template**: Reusable component scaffold
- [ ]² Template testing verifies `dotnet new` works across platforms
- [ ]² NuGet packaging: `MauiScript.Templates` ready for publish

### 1.8 Documentation & Announcement (Weeks 9–10)

- [ ]¹ README.md with quick start using templates, installation, basic example
- [ ]¹ Docs site (VitePress) with:
  - [ ]¹ Landing page (index.md)
  - [ ]¹ Getting Started section:
    - [ ]¹ installation.md (dotnet new install + templates)
    - [ ]¹ hello-world.md (First .maui file)
    - [ ]¹ todo-tutorial.md (Step-by-step Todo App)
  - [ ]¹ Guide section:
    - [ ]¹ syntax.md (Core syntax overview)
    - [ ]¹ bindings.md (@ prefix, converters)
    - [ ]¹ resources.md ($ prefix, themes)
    - [ ]¹ layouts.md (Stack, Grid, Flex)
  - [ ]¹ Reference section:
    - [ ]¹ controls.md (All supported controls)
    - [ ]¹ modifiers.md (All modifiers)
    - [ ]¹ cli.md (mauiscript commands)
- [ ]¹ Blog post draft: "Introducing MauiScript"
- [ ]¹ Comparison screenshots: XAML vs MauiScript side-by-side
- [ ]¹ Video demo: 2-minute screencast of Todo App
- [ ]² GitHub release v0.1.0 tagged with release notes

### Milestone 1 Exit Criteria

- [ ]² `dotnet new mauiscript-app` creates working project
- [ ]⁴ Todo App compiles and runs on iOS + Android simulators
- [ ]¹ Todo App is dogfooded as the primary manual test bed for MauiScript development
- [ ]⁴ All core syntax from spec Phase 1 is implemented
- [ ]² VS Code extension published to marketplace (preview)
- [ ]³ 80%+ test coverage on Parser + Compiler
- [ ]¹ Docs site live with Getting Started + Todo tutorial
- [ ]² NuGet packages published (preview): Compiler, Runtime, Templates
- [ ]⁴ No known blocking bugs

---

## Phase 2: Full Control Library + Settings Sample
**Duration**: 6–8 weeks  
**Goal**: Support all MAUI controls; ship Settings Page sample

### 2.1 Extended Parser

- [ ]³ Grid layout: `Grid[2, 3]`, `Grid["auto, *", "*, *"]`
- [ ]³ Flex layout: `Flex.row.wrap`
- [ ]⁴ Platform blocks: `.iOS { }`, `.android { }`, `.windows { }`
- [ ]³ Inline block syntax: `Button "X" { .command(@Y), .style($Z) }`
- [ ]³ Span syntax: `Text [ span "..." { weight: bold } ]`
- [ ]³ Ref modifier: `.ref(fieldName)`
- [ ]³ Named params: `.padding(horizontal: 24, vertical: 16)`

### 2.2 Extended Code Generator

- [ ]³ All layout types: Grid, FlexLayout, AbsoluteLayout
- [ ]³ All input controls: Editor, Picker, DatePicker, TimePicker, Slider, Stepper, Switch
- [ ]³ All display controls: Image, ImageButton, ActivityIndicator, ProgressBar
- [ ]⁴ Platform conditionals: `#if IOS` / `OnPlatform<>` patterns
- [ ]³ Span/FormattedString: Rich text emission

### 2.3 Settings Page Sample

- [ ]² Grouped sections: Nested Stack/Card hierarchy
- [ ]² Toggle switches: `Switch`, `.toggled(@)`
- [ ]² Sliders: `Slider`, `.value(@)`
- [ ]² Pickers: `Picker`, `.items(@)`, `.selectedIndex(@)`
- [ ]³ Platform blocks: iOS-specific appearance, Android material design
- [ ]³ Nested conditionals: `when @IsPremium` → premium-only settings

### 2.4 Login/Signup Sample

- [ ]² Rich text: Legal disclaimer with tap targets
- [ ]³ Validation errors: Conditional visibility + animations
- [ ]² Secure entry: `.secure(@!ShowPassword)`
- [ ]³ Social buttons: Platform-specific visibility
- [ ]² Loading state: `.loading(@IsLoading)`

---

## Phase 3: Tooling Excellence
**Duration**: 8–10 weeks  
**Goal**: Full LSP implementation; complete CLI toolchain; interactive playground

### 3.1 Language Server

- [ ]³ Completions: After `@` → ViewModel properties; after `$` → resources
- [ ]³ Hover: Documentation for controls and modifiers
- [ ]³ Go to Definition: Jump to ViewModel, resource file
- [ ]³ Find References: Where is `@Email` used?
- [ ]³ Diagnostics: Real-time error squiggles
- [ ]⁴ Code Actions: "Create property in ViewModel", "Extract style"
- [ ]⁴ Rename Symbol: Rename binding across `.maui` and `.cs` files
- [ ]³ Document Symbols: Outline view of page structure
- [ ]³ LSP "Done" Criteria: Completions, hover, diagnostics, and go-to-definition stable against Todo/Settings/Login samples

### 3.2 CLI Tools

- [ ]² `mauiscript doctor`: Environment/workload check
- [ ]² `mauiscript fmt`: Format .maui files (opinionated, like dotnet format)
- [ ]³ `mauiscript migrate`: XAML → MauiScript conversion
- [ ]² `mauiscript validate`: Validate syntax without building
- [ ]³ `mauiscript eject`: MauiScript → C# (escape hatch)
- [ ]² `mauiscript watch`: Watch mode (recompile on save)

### 3.3 Interactive Playground

- [ ]³ Live editor: Edit `.maui` code in left pane
- [ ]³ Generated C# view: See transpiled output in right pane
- [ ]³ Live preview: Render actual MAUI controls (hot reload)
- [ ]³ Error highlighting: Inline diagnostics from parser
- [ ]² Snippet library: Pre-built examples to explore syntax

### 3.4 Error Messages & Developer Experience

- [ ]³ Unknown control: `Error MS1001: Unknown element 'Buttton'. Did you mean 'Button'?`
- [ ]³ Missing ViewModel property: `Warning MS2001: Property 'Emial' not found on LoginViewModel. Did you mean 'Email'?`
- [ ]³ Invalid binding mode: `Error MS1002: Entry.IsEnabled does not support TwoWay binding.`
- [ ]³ Indentation mismatch: `Error MS1003: Unexpected indent level at line 42. Expected 4 spaces, found 6.`
- [ ]³ Deprecated control: `Warning MS2010: 'ListView' is deprecated in MAUI 10. Use 'Collection' instead.`
- [ ]³ Missing resource: `Error MS1004: Resource 'PrmaryButton' not found. Did you mean 'PrimaryButton'?`

---

## Phase 4: Visual Studio Extension
**Duration**: 6–8 weeks  
**Goal**: First-class Visual Studio 2022+ support

### 4.1 Extension Features

- [ ]³ Syntax highlighting: Classifier based on parser
- [ ]³ IntelliSense: Leverage LSP (shared with VS Code)
- [ ]³ Error List integration: Diagnostics in IDE
- [ ]² Solution Explorer: `.maui` files with custom icon
- [ ]³ Build integration: MSBuild props/targets

### 4.2 Project & Item Templates

- [ ]² MauiScript App: "Add New Project" option in VS
- [ ]² MauiScript Page: "Add → New Item → MauiScript Page"
- [ ]² MauiScript Component: "Add → New Item → MauiScript Component"

### 4.3 Stretch Goals

- [ ]³ Hot Reload indicator: Show when regeneration happens
- [ ]³ Side-by-side preview: Generated C# in read-only pane
- [ ]⁴ Design-time preview: Render MAUI controls in preview window (very hard)
- [ ]⁴ Refactoring support: Extract component, inline style

---

## Phase 5: Ecosystem & Advanced Samples
**Duration**: Ongoing  
**Goal**: Community growth; demonstrate complex real-world apps

### 5.1 Product Browser Sample

- [ ]² Virtualized grid: `Collection` with grid layout
- [ ]² Search + filters: SearchBar, filter chips
- [ ]² Master-detail nav: Shell navigation
- [ ]² Image galleries: CarouselView integration
- [ ]² Pull-to-refresh: RefreshView with loading overlay

### 5.2 Chat/Messaging Sample

- [ ]³ Variable-height cells: Complex Collection templates
- [ ]³ Grouped by date: Section headers
- [ ]³ Rich bubbles: Formatted text, images
- [ ]³ Typing indicators: Animated visibility
- [ ]³ Scroll-to-bottom: Element references, `#messageList`

### 5.3 Dashboard Sample

- [ ]³ Third-party charts: `use Charts from "Microcharts.Maui"`
- [ ]³ Stat cards: Custom component composition
- [ ]³ Real-time updates: Binding performance
- [ ]² Quick actions: Grid of action buttons

### 5.4 Dashboard + .NET Aspire Variant (Optional)

- [ ]⁴ Aspire service defaults: `builder.AddServiceDefaults()` integration
- [ ]⁴ Service discovery: Consume backend APIs via Aspire
- [ ]⁴ OpenTelemetry: Distributed tracing from mobile to backend
- [ ]⁴ Multi-project solution: MAUI client + Aspire host + API

### 5.5 Community & Ecosystem

- [ ]² Component library: `MauiScript.Components` NuGet (SocialButton, StatCard, etc.)
- [ ]¹ Third-party control support: Guidance for Syncfusion, Telerik, DevExpress
- [ ]¹ Discord/GitHub Discussions: Community support
- [ ]¹ Contribution guide: CONTRIBUTING.md
- [ ]¹ Awesome MauiScript: Curated list of community resources

---

## Infrastructure & Support

### Build & CI/CD

- [ ]² GitHub Actions CI workflow:
  - [ ]² Build job: restore, build, test with coverage
  - [ ]² Pack job: create NuGet packages
  - [ ]² NuGet publish: on GitHub release
  - [ ]² VS Code extension build and package
  - [ ]² Docs site build and deploy to GitHub Pages
- [ ]² Published Artifacts:
  - [ ]² `MauiScript.*.nupkg` → NuGet.org
  - [ ]² `mauiscript-*.vsix` → VS Marketplace
  - [ ]² Docs site → GitHub Pages

### Documentation

- [ ]¹ Architecture document (ARCHITECTURE.md)
- [ ]³ API reference docs (auto-generated from source)
- [ ]¹ Migration guide from XAML
- [ ]¹ Troubleshooting guide

### Quality & Testing

- [ ]³ Unit test coverage ≥80% for Parser + Compiler
- [ ]³ Integration tests for all sample apps
- [ ]³ Performance benchmarks for parsing and generation
- [ ]² Accessibility testing for generated UI
- [ ]² Security review for CLI tools

---

## Risk Mitigation Status

| Risk | Status | Mitigation |
|------|--------|------------|
| Parser complexity | [ ] | Start with Sprache; move to hand-rolled if perf issues |
| MAUI API churn | [ ] | Target stable .NET 10 LTS; avoid preview features |
| Source generator debugging | [ ] | Use `launch.json` with `debugger` attach; verbose logging |
| Hot reload breaks | [ ] | Validate with each MAUI servicing update |
| Community adoption | [ ] | Focus on excellent docs + comparison guides |
| Template drift | [ ] | Templates tested in CI; version-lock to MauiScript packages |
| MAUI workload issues | [ ] | `mauiscript doctor` catches common setup problems early |
| Breaking syntax changes | [ ] | Spec versioning + migration tooling (`mauiscript migrate --version`) |

---

## Progress Summary

### Phase 1: Todo App MVP
- **Progress**: 0/50 tasks complete
- **Critical Path**: Parser → Code Generator → Todo App → Templates

### Phase 2: Full Control Library
- **Progress**: 0/24 tasks complete
- **Blocked by**: Phase 1 completion

### Phase 3: Tooling Excellence
- **Progress**: 0/23 tasks complete
- **Blocked by**: Phase 1 completion

### Phase 4: Visual Studio Extension
- **Progress**: 0/9 tasks complete
- **Blocked by**: Phase 3 LSP completion

### Phase 5: Ecosystem & Advanced Samples
- **Progress**: 0/22 tasks complete
- **Blocked by**: Phase 2 completion

---

*Last reviewed: December 2025*  
*Next review: Weekly during Phase 1*
