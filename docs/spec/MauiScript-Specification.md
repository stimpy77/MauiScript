# MauiScript: A Modern DSL for .NET MAUI

**Version 0.1.2 — Synthesized from Multi-Agent Consensus (2025)**

> MauiScript is an opinionated, production-ready DSL that replaces XAML for .NET MAUI development. It combines the scalability and tooling-readiness of prefix-based binding notation with the best ergonomic ideas from SwiftUI, Jetpack Compose, React, and modern reactive frameworks.

**Compatibility**: .NET MAUI 10 (.NET 10) and later

**What's New in v0.1.2**: 
- Updated for .NET MAUI 10 compatibility (deprecated controls, async animations, SafeAreaEdges)
- Added comprehensive implementation appendices (binding inference, expression compilation, custom components, attached properties, event handlers, naming/scoping, modules, resource precedence, comments, error examples)
- Three additional page examples (Settings, List/Detail, Dashboard)

---

## Table of Contents

1. [Design Philosophy](#design-philosophy)
2. [Core Syntax Overview](#core-syntax-overview)
3. [Complete Login Page Example](#complete-login-page-example)
4. [Binding System](#binding-system)
5. [Resource System](#resource-system)
6. [Layout & Composition](#layout--composition)
7. [Styled Text & Spans](#styled-text--spans)
8. [Conditional & Dynamic UI](#conditional--dynamic-ui)
9. [Control Modifiers](#control-modifiers)
10. [Animations & Transitions](#animations--transitions)
11. [Naming & References](#naming--references)
12. [Theme & Style Definitions](#theme--style-definitions)
13. [Platform-Specific Code](#platform-specific-code)
14. [Transpilation Strategy](#transpilation-strategy)
15. [Tooling & Developer Experience](#tooling--developer-experience)
16. [Comparison: XAML vs C# Markup vs MauiScript](#comparison-xaml-vs-c-markup-vs-mauiscript)
17. [Trade-offs & Honest Assessment](#trade-offs--honest-assessment)

**Appendices (Implementation Details)**

18. [Appendix A: Binding Inference Rules](#appendix-a-binding-inference-rules)
19. [Appendix B: Expression Binding Compilation](#appendix-b-expression-binding-compilation)
20. [Appendix C: Custom Components](#appendix-c-custom-components)
21. [Appendix D: Attached Properties & Third-Party Controls](#appendix-d-attached-properties--third-party-controls)
22. [Appendix E: Event Handlers (Non-Command)](#appendix-e-event-handlers-non-command)
23. [Appendix F: Naming & Scoping Rules](#appendix-f-naming--scoping-rules)
24. [Appendix G: Module & Import Semantics](#appendix-g-module--import-semantics)
25. [Appendix H: Resource Precedence & Theme Integration](#appendix-h-resource-precedence--theme-integration)
26. [Appendix I: Comments & Documentation](#appendix-i-comments--documentation)
27. [Appendix J: Compile-Time Error Examples](#appendix-j-compile-time-error-examples)
28. [Appendix K: Additional Page Examples](#appendix-k-additional-page-examples)
29. [Appendix L: .NET MAUI 10 Compatibility](#appendix-l-net-maui-10-compatibility)

---

## Design Philosophy

### Why Not Just "XAML Without Angle Brackets"?

The pain points of XAML aren't just the angle brackets—they're:

1. **Excessive ceremony**: Opening/closing tags, namespace declarations, verbose property syntax
2. **Poor signal-to-noise ratio**: The structure obscures the intent
3. **Binding syntax that feels bolted-on**: `{Binding Path=...}` is verbose and inconsistent
4. **Resource references are clunky**: `{StaticResource Key}` interrupts reading flow
5. **No shorthand for common patterns**: Every margin, color, or spacing needs full specification
6. **x:Name pollution**: Naming elements requires explicit ceremony

### The 2025 Rule Set

| Problem with XAML | MauiScript Fix | Inspiration |
|-------------------|----------------|-------------|
| Angle-bracket noise | Zero XML, pure declarative blocks | SwiftUI + Compose |
| Verbose property syntax | Named arguments + modifier chaining | Kotlin DSLs + SwiftUI |
| Boilerplate for layouts | Implicit stacking + `>` chaining | SwiftUI VStack/HStack |
| Binding syntax hell | `@` prefix with pipes for converters | Vue + Svelte + Angular |
| Resources are cryptic | `$ResourceName` sigil | CSS variables + Tailwind |
| x:Name everywhere | Optional `#name` suffix or `ref` modifier | React refs + Compose |
| No shorthand | `.p(16)`, `.m(8)` Tailwind-style utilities | Tailwind CSS |

### Ecosystem Influences

| Source | What We Borrowed | Why |
|--------|------------------|-----|
| **SwiftUI** | Modifier chaining, `.if()` conditionals, declarative composition | Proven ergonomics, beloved by iOS devs |
| **Jetpack Compose** | Function-style components, trailing lambdas for children | Familiar to Android/Kotlin devs |
| **React/JSX** | Component capitalization, props object pattern | Universal web familiarity |
| **Tailwind CSS** | Shorthand utilities (`.p`, `.m`), consistent naming | Reduces cognitive load |
| **Vue/Svelte** | `@` prefix for dynamic values, pipe transforms | Universally understood as "reactive" |
| **MauiFlow** | `.loading()` shorthand, inline resource definitions, animation composition | Real-world pain points solved |
| **Lume** | Enum shorthand (`.Center`), inverted bindings (`@!Property`) | Type inference elegance |

### Core Syntax Decisions

1. **Indentation-based hierarchy** with explicit `{ }` available for complex cases
2. **`@` prefix for bindings** — universally understood as "dynamic/reactive"
3. **`$` prefix for resources** — shell/template string familiarity
4. **Dot-chaining for modifiers** — SwiftUI/Compose pattern, excellent discoverability
5. **Pipe `|` for converters** — Unix/Angular/Vue convention
6. **`>` operator for single-child chaining** — eliminates wrapper noise
7. **Two-way binding is default** for form inputs — the 90% case

---

## Core Syntax Overview

### File Structure

```mauiscript
// login.maui
// File extension: .maui or .ms

use Theme from "@app/resources"
use Validators from "@app/validation"

page LoginPage
  @viewmodel: LoginViewModel
  @title: "Welcome Back"
  
  // Content starts here (indentation-based)
  Stack.vertical.spacing(24)
    Text "Hello World"
```

### Element Declaration

```mauiscript
// Basic element
Button "Click me"

// With modifiers (SwiftUI-style chaining)
Button "Click me"
  .style($PrimaryButton)
  .command(@LoginCommand)

// With children (indentation)
Stack.vertical
  Text "Item 1"
  Text "Item 2"

// Single-child chaining (eliminates wrapper noise)
SafeArea > Scroll > Stack.vertical
  Text "Content"

// Inline block form (for compact definitions)
Button "Submit" { .command(@Save), .style($Primary) }
```

### Property Syntax

```mauiscript
// Literal values
.text("Hello")
.size(120)
.padding(16)
.visible(true)

// Named arguments (CSS-like)
.padding(horizontal: 24, vertical: 16)
.font(size: 18, weight: bold)
.margin(top: 8, bottom: 16)

// Shorthand (Tailwind-inspired)
.p(16)              // padding: 16 all sides
.p(8, 16)           // padding: vertical 8, horizontal 16
.m(top: 8)          // margin-top: 8
.size(48)           // width: 48, height: 48
```

---

## Complete Login Page Example

This example demonstrates every major feature of MauiScript in a production-ready login page:

```mauiscript
// login.maui
// A complete login page with validation, binding, styled text, and interactions

use Theme from "@app/resources"
use Validators from "@app/validation"

page LoginPage
  @viewmodel: LoginViewModel
  @title: "Welcome Back"
  @background: $Surface
  
  // Page-scoped resources (merged with app resources)
  resources
    CornerRadius: 12
    InputHeight: 56
    CardShadow: shadow(4, color: #00000020)
  
  // Main layout: safe area aware, scrollable, centered
  SafeArea > Scroll
    
    Stack.vertical
      .spacing(24)
      .padding(horizontal: 24, vertical: 40)
      .center
      
      // ═══════════════════════════════════════════════════════
      // HEADER SECTION
      // ═══════════════════════════════════════════════════════
      
      Image("logo.png")
        .size(100)
        .tint($AccentColor)
        .center
        .tap(@EasterEggCommand)
      
      // Styled inline text with multiple formats
      Text [
        span "Welcome to " { weight: bold }
        span "MauiApp" { color: $AccentColor, weight: bold }
      ]
        .font(size: 28)
        .center
      
      Text "Sign in to continue"
        .font(size: 16)
        .color($TextSecondary)
        .center
        .m(bottom: 16)
      
      // ═══════════════════════════════════════════════════════
      // FORM CARD
      // ═══════════════════════════════════════════════════════
      
      Card
        .padding(20)
        .corner($CornerRadius)
        .shadow($CardShadow)
        .background($CardBackground)
        
        Stack.vertical.spacing(16)
          
          // Email field with validation
          Entry #emailField
            .placeholder("Email address")
            .keyboard(email)
            .text(@Email)                           // two-way by default for Entry
            .valid(@EmailIsValid)
            .autocapitalize(none)
            .style($InputStyle)
          
          // Email validation error (conditional visibility + animation)
          Text @EmailError
            .color($ErrorColor)
            .font(size: 12)
            .visible(@EmailError | isNotEmpty)
            .animate(fade + slideDown, duration: 200)
          
          // Password field with visibility toggle
          Stack.horizontal.spacing(8)
            
            Entry #passwordField
              .placeholder("Password")
              .secure(@!ShowPassword)               // inverted binding
              .text(@Password)
              .returnType(go)
              .onReturn(@LoginCommand)
              .style($InputStyle)
              .flex(1)
            
            ImageButton
              .image(@ShowPassword | passwordIcon)  // converter
              .size(44)
              .tap(@TogglePasswordCommand)
              .background(transparent)
          
          // Remember me + Forgot password row
          Stack.horizontal
            .justify(spaceBetween)
            .align(centerVertical)
            
            Stack.horizontal.spacing(8).align(centerVertical)
              CheckBox @RememberMe
              Text "Remember me"
                .font(size: 14)
                .tap(toggle: @RememberMe)
            
            Text "Forgot password?"
              .color($AccentColor)
              .font(size: 14, weight: medium)
              .tap(@ForgotPasswordCommand)
          
          // ═══════════════════════════════════════════════════════
          // ERROR DISPLAY (Conditional block)
          // ═══════════════════════════════════════════════════════
          
          when @HasError
            Stack.horizontal.spacing(8)
              .padding(12)
              .background($ErrorColor | alpha(0.1))
              .corner(8)
              
              Icon("alert-circle")
                .size(20)
                .color($ErrorColor)
              
              Text @ErrorMessage
                .color($ErrorColor)
                .font(size: 14)
                .flex(1)
            
            .animate(fade + slideDown)
          
          // ═══════════════════════════════════════════════════════
          // PRIMARY ACTION
          // ═══════════════════════════════════════════════════════
          
          Button "Sign In"
            .style($PrimaryButton)
            .height($InputHeight)
            .corner($CornerRadius)
            .command(@LoginCommand)
            .enabled(@CanLogin)
            .loading(@IsLoading)                    // built-in loading state
      
      // ═══════════════════════════════════════════════════════
      // DIVIDER
      // ═══════════════════════════════════════════════════════
      
      Stack.horizontal.spacing(16).center.m(top: 8)
        Divider.flex(1).color($Border)
        Text "or continue with"
          .color($TextTertiary)
          .font(size: 14)
        Divider.flex(1).color($Border)
      
      // ═══════════════════════════════════════════════════════
      // SOCIAL LOGIN
      // ═══════════════════════════════════════════════════════
      
      Stack.horizontal.spacing(12).center
        
        SocialButton("google")
          .command(@GoogleLoginCommand)
        
        SocialButton("apple")
          .command(@AppleLoginCommand)
          .iOS { .visible(true) }                   // platform-specific
          .android { .visible(false) }
        
        SocialButton("microsoft")
          .command(@MicrosoftLoginCommand)
      
      // ═══════════════════════════════════════════════════════
      // FOOTER
      // ═══════════════════════════════════════════════════════
      
      Stack.horizontal.spacing(4).center.m(top: 24)
        Text "Don't have an account?"
          .font(size: 14)
          .color($TextSecondary)
        Text "Sign up"
          .font(size: 14, weight: semibold)
          .color($AccentColor)
          .underline
          .tap(@NavigateToSignUpCommand)
      
      // Complex rich text with multiple tap targets
      Text [
        span "By signing in, you agree to our "
        span "Terms of Service" {
          color: $AccentColor
          underline: true
          tap: @ShowTermsCommand
        }
        span " and "
        span "Privacy Policy" {
          color: $AccentColor
          underline: true
          tap: @ShowPrivacyCommand
        }
      ]
        .font(size: 12)
        .color($TextTertiary)
        .center
        .lineHeight(1.5)
        .m(top: 32)
```

---

## Binding System

### Core Binding Notation

The `@` prefix denotes any dynamic/bound value. This is universally recognized from Vue (`v-bind`), Angular (`@Input`), Svelte (`$:`), and decorators.

```mauiscript
// One-way binding (source → UI)
.text(@UserName)
.visible(@IsLoggedIn)
.color(@StatusColor)

// Two-way binding (explicit mode)
.text(@Email, mode: twoWay)

// Two-way is DEFAULT for form inputs (Entry, Editor, CheckBox, etc.)
Entry
  .text(@Email)              // automatically two-way for Entry
  
CheckBox @RememberMe         // shorthand: binds IsChecked two-way
```

### Why `@` Instead of Arrows?

During the design debate, arrow operators (`<->`, `<-`, `=>`) were considered. While visually elegant in small examples, they create scanning problems in large files:

```mauiscript
// ❌ Arrow soup in a 50+ binding page (hard to scan)
text <-> vm.Email
text <-> vm.Password  
isEnabled <- vm.CanLogin
isVisible <- vm.HasError
command <- vm.Login
onTap => vm.ForgotPassword

// ✅ Consistent prefix (easy to scan, one character anchor)
.text(@Email)
.text(@Password)
.enabled(@CanLogin)
.visible(@HasError)
.command(@LoginCommand)
.tap(@ForgotPasswordCommand)
```

The `@` prefix provides a calm, columnar, instantly scannable pattern even in 300-line pages.

### Converters (Pipe Syntax)

Converters use the pipe `|` operator, familiar from Unix, Angular, and Vue:

```mauiscript
// Built-in converters
.visible(@Items | hasAny)              // true if collection has items
.visible(@ErrorMessage | isNotEmpty)   // true if string not empty
.text(@Price | format: "C2")           // currency format
.color(@Status | statusToColor)        // custom converter
.image(@ShowPassword | passwordIcon)   // conditional image

// Chained converters
.text(@LastUpdated | toLocal | format: "MMM d, yyyy")

// Negation shorthand
.visible(@!IsLoading)                  // same as @IsLoading | not
.secure(@!ShowPassword)                // inverted binding
```

### Command Binding

```mauiscript
// Simple command
.command(@LoginCommand)

// Command with parameter
.command(@DeleteCommand, param: @SelectedItem)

// Inline command from tap gesture
.tap(@ShowDetailsCommand)
.tap(@DeleteCommand, param: @item.Id)

// Event-to-command (for non-command events)
.onReturn(@LoginCommand)
.onFocus(@TrackFocusCommand)
.onTextChanged(@ValidateCommand)
```

### Binding Expressions

For complex expressions, use the `expr` helper:

```mauiscript
// Simple boolean logic
.enabled(@IsValid and not @IsLoading)
.visible(@Items | hasAny or @ShowEmpty)

// Ternary-style conditional
.text(@IsEditing | then: "Save" | else: "Edit")
.color(@IsError | then: $ErrorColor | else: $TextPrimary)
```

---

## Resource System

### Resource References

The `$` prefix denotes resource references (StaticResource by default):

```mauiscript
// Static resource (most common)
.color($AccentColor)
.style($PrimaryButton)
.corner($CornerRadius)

// Dynamic resource (theme-reactive)
.color($$ThemeBackground)             // double $ for dynamic
.background($$SurfaceColor)

// Inline values
.color(#3B82F6)                        // hex color
.color(rgb(59, 130, 246))              // RGB
.color(blue.500)                       // named palette
```

### Page-Scoped Resources

```mauiscript
page SettingsPage
  @viewmodel: SettingsViewModel
  
  resources
    CardPadding: 16
    HeaderHeight: 64
    CustomAccent: #8B5CF6
    InputStyle: Style(Entry) {
      .background($Surface)
      .corner(8)
      .padding(12)
      .height(48)
    }
  
  // Use in page
  Card.p($CardPadding)
    Entry.style($InputStyle)
```

### Inline Resource Expressions

```mauiscript
// Color with alpha
.background($ErrorColor | alpha(0.1))
.shadow(4, color: $AccentColor | alpha(0.3))

// Gradient
.background(gradient(vertical, $GradientStart, $GradientEnd))
.background(gradient(#0f172a, #1e293b))

// Computed values
.corner($CornerRadius | multiply(2))
```

---

## Layout & Composition

### Stack Layouts

```mauiscript
// Vertical stack (most common)
Stack.vertical
Stack.vertical.spacing(16)
Stack.v.spacing(16)                    // shorthand

// Horizontal stack
Stack.horizontal
Stack.horizontal.spacing(8)
Stack.h.spacing(8)                     // shorthand

// With alignment and distribution
Stack.v.spacing(16).center
Stack.h.spacing(8).justify(spaceBetween)
Stack.h.spacing(8).align(centerVertical)
```

### Single-Child Chaining

The `>` operator eliminates wrapper noise for single-child relationships:

```mauiscript
// Without chaining (verbose)
SafeArea
  Scroll
    Stack.vertical
      Content...

// With chaining (clean)
SafeArea > Scroll > Stack.vertical
  Content...

// Real-world example
RefreshView(@RefreshCommand) > Scroll > Stack.v.spacing(16).p(24)
  // page content
```

### Grid Layout

```mauiscript
// Simple grid (row/column count)
Grid[2, 3]                             // 2 rows, 3 columns
  Text "Cell 1"
  Text "Cell 2"
  // ... cells flow left-to-right, top-to-bottom

// Explicit definitions
Grid["auto, *, auto", "*, *"]          // row defs, column defs
  Text "Header".row(0).columnSpan(2)
  Text "Sidebar".row(1).col(0)
  Text "Content".row(1).col(1)

// Responsive grid
Grid.columns("* * *")                  // 3 equal columns
  .iOS { .columns("* *") }             // 2 columns on phone
```

### Flex Layout

```mauiscript
Flex.row.wrap
  .justify(spaceAround)
  .align(stretch)
  
  foreach @Tags as tag
    Chip(@tag.Name)
      .m(4)
```

### Common Layout Patterns

```mauiscript
// Centered content
Stack.v.center
  Content...

// Fill available space
Stack.h
  Text "Label"
  Spacer                               // pushes next element right
  Text "Value"

// Proportional sizing
Stack.h
  Box.flex(1).background($Primary)
  Box.flex(2).background($Secondary)   // twice as wide
```

---

## Styled Text & Spans

### Simple Text

```mauiscript
Text "Hello World"
  .font(size: 18, weight: bold)
  .color($TextPrimary)

// Bound text
Text @UserName
  .font(size: 24)

// With line formatting
Text @Description
  .lineHeight(1.6)
  .maxLines(3)
  .truncation(tail)
```

### Complex Formatted Text (FormattedString Replacement)

```mauiscript
// Multi-style text with bracket syntax
Text [
  span "Regular text "
  span "bold text" { weight: bold }
  span "colored" { color: $AccentColor }
  span "linked" {
    color: $LinkColor
    underline: true
    tap: @HandleLinkCommand
  }
]
  .lineHeight(1.5)

// Interpolated text with bound values
Text [
  span "Hello, "
  span @UserName { weight: bold }
  span "! You have "
  span @NotificationCount { 
    color: $AccentColor
    weight: bold 
  }
  span " new messages."
]

// Multi-line with explicit breaks
Text [
  span "First line"
  span "\n"
  span "Second line" { style: italic }
]
```

### Rich Text with Gestures

```mauiscript
Text [
  span "By continuing, you agree to our "
  span "Terms of Service" {
    color: $AccentColor
    underline: true
    tap: @ShowTermsCommand
  }
  span " and "
  span "Privacy Policy" {
    color: $AccentColor
    underline: true
    tap: @ShowPrivacyCommand
  }
  span "."
]
  .font(size: 12)
  .color($TextSecondary)
  .center
```

---

## Conditional & Dynamic UI

### Visibility Binding

```mauiscript
// Simple visibility
Text "Error occurred"
  .visible(@HasError)

// With converter
Button "Submit"
  .visible(@Items | hasAny)
  .enabled(@IsValid and not @IsLoading)

// Inverted visibility
LoadingSpinner
  .visible(@IsLoading)

Content
  .visible(@!IsLoading)                // visible when NOT loading
```

### Conditional Blocks (when/else)

```mauiscript
// Binary condition
when @IsLoggedIn
  Text "Welcome back, {@UserName}!"
else
  Button "Sign In"
    .command(@ShowLoginCommand)

// With animation
when @HasError
  ErrorBanner(@ErrorMessage)
    .animate(slideDown)
else
  // nothing rendered
```

### Switch/Match Pattern

```mauiscript
match @UserRole
  case "admin"
    AdminDashboard
  case "moderator"
    ModeratorDashboard
  case "user"
    UserDashboard
  default
    GuestView

// Match with binding
match @ConnectionState
  case "connected"
    Icon("wifi").color($SuccessColor)
  case "connecting"
    ActivityIndicator.size(small)
  case "disconnected"
    Icon("wifi-off").color($ErrorColor)
```

### Collection Rendering

```mauiscript
// Simple foreach
foreach @Items as item
  Card.m(bottom: 8)
    Text @item.Title
      .font(weight: bold)
    Text @item.Description
      .color($SecondaryText)

// With index
foreach @Items as item, index
  Text "{@index + 1}. {@item.Name}"

// Empty state
foreach @Items as item
  ItemCard(@item)
empty
  EmptyState("No items found")
    .center

// CollectionView equivalent (virtualized)
Collection(@Items)
  .layout(vertical, spacing: 8)
  .template(item =>
    ItemCard(@item)
  )
```

---

## Control Modifiers

### Conditional Modifiers

```mauiscript
Entry
  .placeholder("Search...")
  .text(@SearchQuery)
  .keyboard(search)
  
  // Conditional modifier block
  .if(@IsSearching) {
    .opacity(0.5)
    .enabled(false)
  }
  
  // Ternary-style conditional
  .background(@IsFocused | then: $FocusedBg | else: $NormalBg)
```

### Loading State (Built-in Pattern)

```mauiscript
// The .loading() modifier handles the common pattern:
// - Disables button when loading
// - Replaces content with ActivityIndicator
// - Restores when loading complete

Button "Sign In"
  .command(@LoginCommand)
  .loading(@IsLoading)

// Equivalent to:
Button
  .enabled(@!IsLoading)
  .content(
    when @IsLoading
      ActivityIndicator.color(White)
    else
      Text "Sign In"
  )
```

### State Modifiers

```mauiscript
Button "Submit"
  .style($PrimaryButton)
  
  .pressed {
    .scale(0.98)
    .opacity(0.9)
  }
  
  .disabled {
    .opacity(0.5)
  }
  
  .focused {
    .border(2, $AccentColor)
  }
```

---

## Animations & Transitions

### Built-in Transitions

```mauiscript
// Visibility transitions
Text @StatusMessage
  .visible(@ShowStatus)
  .animate(fade, duration: 300)

// Entry animations
Card
  .animate(slideUp, delay: 100)
  .animate(fade)

// Combined animations
ErrorBanner
  .visible(@HasError)
  .animate(fade + slideDown, duration: 200)
```

### State-Based Animations

```mauiscript
Button "Submit"
  .scale(when: @IsPressed, value: 0.95, duration: 100)
  .opacity(when: @IsDisabled, value: 0.5)
  .background(when: @IsHovered, value: $HoverColor)
```

### Custom Animations

```mauiscript
Image("hero.png")
  .animate {
    from { opacity: 0, translateY: 20 }
    to { opacity: 1, translateY: 0 }
    duration: 500
    easing: easeOut
    delay: 100
  }

// Keyframe animation
Logo
  .animate {
    keyframes {
      0% { scale: 1 }
      50% { scale: 1.1 }
      100% { scale: 1 }
    }
    duration: 2000
    repeat: infinite
  }
```

---

## Naming & References

### Element Naming

When you need to reference an element (equivalent to `x:Name`), use the `#name` suffix:

```mauiscript
// Named element
Entry #emailField
  .placeholder("Email")
  .text(@Email)

Entry #passwordField
  .placeholder("Password")
  .text(@Password)

// Reference in code-behind or for focus management
Button "Next"
  .tap(focus: #passwordField)
```

### Ref Modifier (Alternative)

```mauiscript
// Using ref modifier (creates field in generated code)
Entry
  .ref(emailEntry)
  .text(@Email)

// Later accessible as this.emailEntry in C#
```

### When to Name Elements

```mauiscript
// ✅ Name when you need programmatic access
Entry #searchField              // for focus management
CollectionView #itemsList       // for scroll-to-item

// ❌ Don't name just for binding (use @binding instead)
Entry.text(@Email)              // no name needed
```

---

## Theme & Style Definitions

### Resource File Structure

```mauiscript
// theme.maui — App-wide resource dictionary

resources AppTheme

  // ═══════════════════════════════════════════════════════
  // COLORS
  // ═══════════════════════════════════════════════════════
  
  colors
    // Brand
    AccentColor: #3B82F6
    AccentColorDark: #2563EB
    AccentColorLight: #60A5FA
    
    // Text
    TextPrimary: #1F2937
    TextSecondary: #6B7280
    TextTertiary: #9CA3AF
    TextOnAccent: #FFFFFF
    
    // Surfaces
    Background: #FFFFFF
    Surface: #F9FAFB
    CardBackground: #FFFFFF
    
    // Semantic
    ErrorColor: #EF4444
    SuccessColor: #10B981
    WarningColor: #F59E0B
    
    // Borders & Dividers
    Border: #E5E7EB
    Divider: #F3F4F6
    
    // Dark mode overrides
    @dark
      TextPrimary: #F9FAFB
      TextSecondary: #D1D5DB
      TextTertiary: #9CA3AF
      Background: #111827
      Surface: #1F2937
      CardBackground: #1F2937
      Border: #374151
      Divider: #1F2937

  // ═══════════════════════════════════════════════════════
  // TYPOGRAPHY
  // ═══════════════════════════════════════════════════════
  
  fonts
    DisplayLarge: { size: 32, weight: bold, family: "Inter" }
    HeadingLarge: { size: 28, weight: bold }
    HeadingMedium: { size: 24, weight: semibold }
    HeadingSmall: { size: 20, weight: semibold }
    BodyLarge: { size: 18, weight: regular, lineHeight: 1.5 }
    Body: { size: 16, weight: regular, lineHeight: 1.5 }
    BodySmall: { size: 14, weight: regular, lineHeight: 1.4 }
    Caption: { size: 12, weight: medium, color: $TextSecondary }
    
  // ═══════════════════════════════════════════════════════
  // SPACING & SIZING
  // ═══════════════════════════════════════════════════════
  
  spacing
    xs: 4
    sm: 8
    md: 16
    lg: 24
    xl: 32
    
  sizing
    CornerRadius: 8
    CornerRadiusLarge: 16
    ButtonHeight: 48
    InputHeight: 48
    IconSize: 24
    IconSizeSmall: 20
    IconSizeLarge: 32

  // ═══════════════════════════════════════════════════════
  // COMPONENT STYLES
  // ═══════════════════════════════════════════════════════
  
  styles
    
    PrimaryButton: Button
      .background($AccentColor)
      .color($TextOnAccent)
      .font(size: 16, weight: semibold)
      .padding(vertical: 14, horizontal: 24)
      .corner($CornerRadius)
      .height($ButtonHeight)
      
      .pressed {
        .background($AccentColorDark)
      }
      
      .disabled {
        .opacity(0.5)
      }
    
    SecondaryButton: Button
      .background(transparent)
      .color($AccentColor)
      .border(1, $AccentColor)
      .font(size: 16, weight: medium)
      .padding(vertical: 14, horizontal: 24)
      .corner($CornerRadius)
      .height($ButtonHeight)
      
      .pressed {
        .background($AccentColor | alpha(0.1))
      }
    
    GhostButton: Button
      .background(transparent)
      .color($AccentColor)
      .font(size: 16, weight: medium)
      .padding(vertical: 14, horizontal: 24)
      
      .pressed {
        .background($AccentColor | alpha(0.05))
      }
    
    InputStyle: Entry
      .background($Surface)
      .color($TextPrimary)
      .placeholder(color: $TextTertiary)
      .padding(16)
      .corner($CornerRadius)
      .border(1, $Border)
      .height($InputHeight)
      
      .focused {
        .border(2, $AccentColor)
      }
      
      .invalid {
        .border(1, $ErrorColor)
      }
    
    Card: Frame
      .background($CardBackground)
      .corner($CornerRadiusLarge)
      .shadow(4, color: #00000010)
      .border(1, $Border)
      .padding(16)
```

---

## Platform-Specific Code

### Inline Platform Blocks

```mauiscript
Button "Share"
  .style($PrimaryButton)
  
  .iOS {
    .corner(25)                        // pill shape on iOS
    .shadow(none)
  }
  
  .android {
    .corner(8)                         // material style
    .shadow(4)
  }
  
  .windows {
    .corner(4)                         // windows style
  }
```

### Platform Visibility

```mauiscript
// Show only on specific platform
SocialButton("apple")
  .visible(Device.iOS or Device.macOS)

SocialButton("google")
  .visible(Device.android)

// Platform-specific layout
Stack.h.spacing(Device.iOS ? 16 : 8)
  Content...
```

### Device Capabilities

```mauiscript
// Check capabilities
BiometricButton
  .visible(Device.hasBiometrics)
  .text(Device.biometricType | biometricLabel)  // "Face ID" or "Fingerprint"

CameraButton
  .visible(Device.hasCamera)
```

---

## Transpilation Strategy

### Target: C# Fluent Markup

MauiScript compiles to C# code (not XAML), leveraging clean fluent builder patterns:

#### Input (MauiScript)

```mauiscript
Stack.vertical.spacing(16).padding(24)
  
  Text "Hello"
    .font(size: 24, weight: bold)
    .color($AccentColor)
  
  Entry
    .placeholder("Email")
    .text(@Email)
    .keyboard(email)
    .style($InputStyle)
  
  Button "Submit"
    .command(@SubmitCommand)
    .style($PrimaryButton)
    .loading(@IsLoading)
```

#### Output (Generated C#)

```csharp
// LoginPage.g.cs (generated, do not edit)

public partial class LoginPage : ContentPage
{
    public LoginPage(LoginViewModel viewModel)
    {
        BindingContext = viewModel;
        
        Content = new VerticalStackLayout
        {
            Spacing = 16,
            Padding = new Thickness(24),
            Children =
            {
                new Label()
                    .Text("Hello")
                    .FontSize(24)
                    .FontAttributes(FontAttributes.Bold)
                    .DynamicResource(Label.TextColorProperty, "AccentColor"),
                
                new Entry()
                    .Placeholder("Email")
                    .Bind(Entry.TextProperty, nameof(viewModel.Email), BindingMode.TwoWay)
                    .Keyboard(Keyboard.Email)
                    .DynamicResource(Entry.StyleProperty, "InputStyle"),
                
                new Button()
                    .Text("Submit")
                    .BindCommand(nameof(viewModel.SubmitCommand))
                    .DynamicResource(Button.StyleProperty, "PrimaryButton")
                    .WithLoadingState(
                        new Binding(nameof(viewModel.IsLoading))
                    )
            }
        };
    }
}
```

### Why C# Output (Not XAML)?

1. **Hot reload works** — C# hot reload in MAUI is mature
2. **Type safety** — Compile-time errors instead of runtime XAML parsing errors
3. **Better debugging** — Step through your UI code
4. **No XAML parser overhead** — Faster startup
5. **Easier extension** — Custom components are just C# methods

### Optional XAML Output

For teams that need designer support, MauiScript can optionally emit XAML:

```xml
<!-- LoginPage.xaml (generated) -->
<ContentPage xmlns="..." x:Class="App.LoginPage">
    <VerticalStackLayout Spacing="16" Padding="24">
        <Label Text="Hello" 
               FontSize="24" 
               FontAttributes="Bold"
               TextColor="{DynamicResource AccentColor}" />
        <!-- ... -->
    </VerticalStackLayout>
</ContentPage>
```

---

## Tooling & Developer Experience

### VS Code Extension

```json
{
  "name": "mauiscript",
  "displayName": "MauiScript",
  "contributes": {
    "languages": [{
      "id": "mauiscript",
      "extensions": [".maui", ".ms"],
      "aliases": ["MauiScript", "maui"],
      "configuration": "./language-configuration.json"
    }],
    "grammars": [{
      "language": "mauiscript",
      "scopeName": "source.mauiscript",
      "path": "./syntaxes/mauiscript.tmLanguage.json"
    }]
  }
}
```

### IntelliSense Features

1. **Component completion**: Type `Bu` → suggests `Button`, `Border`, `BoxView`
2. **Modifier completion**: After `.` → suggests available modifiers for that component
3. **Binding completion**: After `@` → suggests ViewModel properties and commands
4. **Resource completion**: After `$` → suggests defined resources
5. **Converter completion**: After `|` → suggests available converters
6. **Enum completion**: Keyboard types, font weights, layout options
7. **Hover documentation**: Show MAUI documentation for each element
8. **Go to definition**: Navigate to ViewModel commands/properties, resource definitions
9. **Error squiggles**: Invalid bindings, missing resources, type mismatches
10. **Quick fixes**: "Create property 'Foo' in ViewModel", "Create resource 'Bar'"

### Build Integration

```xml
<!-- .csproj addition -->
<ItemGroup>
  <MauiScript Include="**/*.maui" />
</ItemGroup>

<PackageReference Include="MauiScript.Compiler" Version="1.0.0" />
```

The compiler runs as a Roslyn Source Generator, producing C# at build time with:
- Full incremental compilation support
- Error messages pointing to `.maui` file line numbers
- Source-mapped debugging

### Hot Reload

Because MauiScript generates C# (not XAML), standard .NET MAUI hot reload works:

1. Save `.maui` file
2. Source generator updates `.g.cs`
3. Hot reload applies changes
4. UI updates without restart

### Migration Tool

```bash
# One-click conversion of existing XAML
dotnet tool install -g MauiScript.Migrator
mauiscript migrate ./Views/*.xaml
```

---

## Comparison: XAML vs C# Markup vs MauiScript

### Same UI in Three Syntaxes

#### XAML (Traditional)

```xml
<VerticalStackLayout Spacing="16" Padding="24">
    <Label Text="Hello"
           FontSize="24"
           FontAttributes="Bold"
           TextColor="{DynamicResource AccentColor}" />
    
    <Entry Placeholder="Enter email"
           Text="{Binding Email, Mode=TwoWay}"
           Keyboard="Email">
        <Entry.Behaviors>
            <toolkit:EmailValidatorBehavior />
        </Entry.Behaviors>
    </Entry>
    
    <Label Text="{Binding EmailError}"
           TextColor="{DynamicResource ErrorColor}"
           FontSize="12"
           IsVisible="{Binding EmailError, 
                       Converter={StaticResource StringNotEmptyConverter}}" />
    
    <Button Text="Submit"
            Command="{Binding SubmitCommand}"
            Style="{DynamicResource PrimaryButton}"
            IsEnabled="{Binding CanSubmit}" />
</VerticalStackLayout>
```

#### C# Markup (Current Alternative)

```csharp
new VerticalStackLayout { Spacing = 16, Padding = 24 }.Children(
    new Label()
        .Text("Hello")
        .FontSize(24)
        .Bold()
        .DynamicResource(Label.TextColorProperty, "AccentColor"),
    
    new Entry()
        .Placeholder("Enter email")
        .Bind(Entry.TextProperty, "Email", BindingMode.TwoWay)
        .Keyboard(Keyboard.Email)
        .Behaviors(new EmailValidatorBehavior()),
    
    new Label()
        .Bind(Label.TextProperty, "EmailError")
        .DynamicResource(Label.TextColorProperty, "ErrorColor")
        .FontSize(12)
        .Bind(Label.IsVisibleProperty, "EmailError", 
              converter: new StringNotEmptyConverter()),
    
    new Button()
        .Text("Submit")
        .BindCommand("SubmitCommand")
        .DynamicResource(Button.StyleProperty, "PrimaryButton")
        .Bind(Button.IsEnabledProperty, "CanSubmit")
);
```

#### MauiScript

```mauiscript
Stack.vertical.spacing(16).p(24)
  
  Text "Hello"
    .font(size: 24, weight: bold)
    .color($AccentColor)
  
  Entry
    .placeholder("Enter email")
    .text(@Email)
    .keyboard(email)
    .validate(email)
  
  Text @EmailError
    .color($ErrorColor)
    .font(size: 12)
    .visible(@EmailError | isNotEmpty)
  
  Button "Submit"
    .command(@SubmitCommand)
    .style($PrimaryButton)
    .enabled(@CanSubmit)
```

### Metrics

| Metric | XAML | C# Markup | MauiScript |
|--------|------|-----------|------------|
| Lines | 25 | 22 | 16 |
| Characters | ~1,100 | ~950 | ~400 |
| Nested depth | 3 | 2 | 1 |
| Binding verbosity | High | Medium | Low |
| Resource verbosity | High | Medium | Low |

---

## Trade-offs & Honest Assessment

### Advantages

- **60% reduction in code volume** for typical UI
- **Familiar to developers from multiple ecosystems** — SwiftUI, Compose, React, Vue
- **Strong typing preserved** through C# compilation
- **No runtime parsing** — all errors at build time
- **Gradual adoption** — can coexist with XAML/C# Markup in same project
- **Hot reload works** — generates C#, not XAML
- **Excellent tooling story** — IntelliSense, go-to-definition, error squiggles

### Disadvantages

- **New syntax to learn** — even if intuitive, it's still new
- **Tooling investment required** — needs VS/VSCode extensions
- **Indentation sensitivity** — some developers dislike this (Python debates)
- **Community building** — documentation, Stack Overflow answers, tutorials
- **MAUI feature lag** — new MAUI features need DSL updates
- **Designer support** — visual designers may not work directly with `.maui` files

### What This Isn't

- **Not a runtime framework** — it's a compile-time transformation
- **Not a replacement for MVVM** — works with existing ViewModels
- **Not platform-specific** — generates standard MAUI code
- **Not locked-in** — can eject to C# at any time (run `mauiscript eject`)

### Migration Path

1. **Coexistence**: MauiScript files work alongside existing XAML
2. **Incremental**: Convert one page at a time
3. **Reversible**: Generated C# is readable and maintainable
4. **Tooling**: `mauiscript migrate` converts XAML → MauiScript

---

## Implementation Roadmap

### Phase 1: Core Language (Months 1-3)
- Parser for basic elements, modifiers, bindings
- C# code generation for Stack, Text, Button, Entry, Image
- VS Code syntax highlighting

### Phase 2: Full Control Library (Months 4-6)
- All MAUI controls supported
- Resource system (`$` references)
- Style definitions
- Collection rendering (`foreach`)

### Phase 3: Tooling (Months 7-9)
- VS Code Language Server (full IntelliSense)
- Error diagnostics with `.maui` line numbers
- Go-to-definition for bindings and resources

### Phase 4: Visual Studio Extension (Months 10-12)
- Full VS integration
- Hot reload support
- Design-time preview (stretch goal)

### Phase 5: Ecosystem (Ongoing)
- XAML → MauiScript migration tool
- Documentation site
- Community components
- Third-party control library support

---

## Conclusion

MauiScript is not "XAML without angle brackets." It's a complete rethinking of how developers want to express UI composition in 2025:

- **Scalable**: The `@`/`$` prefix system stays readable in 300-line files
- **Complete**: Handles every real-world scenario from simple forms to complex conditional rendering
- **Familiar**: Borrows proven patterns from SwiftUI, Compose, React, and CSS
- **Practical**: Generates clean C# that integrates with existing MAUI tooling
- **Honest**: Acknowledges trade-offs and provides escape hatches

The goal is a tool that makes developers say "why would I ever go back to XAML?" while being pragmatic enough to actually ship and maintain.

---

# Appendices: Implementation Details

The following appendices provide the precise rules needed to implement a MauiScript compiler. These address edge cases, disambiguation rules, and compilation strategies.

---

## Appendix A: Binding Inference Rules

The compiler uses control type and property metadata to determine binding modes automatically.

### Two-Way Binding Defaults

The following control+property combinations default to **TwoWay** binding when using `@`:

| Control | Property | Binding Syntax | Default Mode |
|---------|----------|----------------|--------------|
| `Entry` | `text` | `.text(@Email)` | TwoWay |
| `Entry` | `text` | `.text(@Email, mode: oneWay)` | OneWay (explicit) |
| `Editor` | `text` | `.text(@Notes)` | TwoWay |
| `CheckBox` | `isChecked` | `CheckBox @RememberMe` | TwoWay |
| `CheckBox` | `isChecked` | `.checked(@RememberMe)` | TwoWay |
| `Switch` | `isToggled` | `.toggled(@DarkMode)` | TwoWay |
| `Slider` | `value` | `.value(@Volume)` | TwoWay |
| `Stepper` | `value` | `.value(@Quantity)` | TwoWay |
| `DatePicker` | `date` | `.date(@BirthDate)` | TwoWay |
| `TimePicker` | `time` | `.time(@AlarmTime)` | TwoWay |
| `Picker` | `selectedIndex` | `.selectedIndex(@Choice)` | TwoWay |
| `SearchBar` | `text` | `.text(@SearchQuery)` | TwoWay |

### One-Way Binding Defaults

All other control+property combinations default to **OneWay**:

| Control | Property | Binding Syntax | Default Mode |
|---------|----------|----------------|--------------|
| `Label` | `text` | `.text(@UserName)` | OneWay |
| `Label` | `text` | `Text @UserName` | OneWay |
| `Image` | `source` | `.source(@AvatarUrl)` | OneWay |
| `Button` | `text` | `Button @ButtonLabel` | OneWay |
| `Button` | `isEnabled` | `.enabled(@CanSubmit)` | OneWay |
| `*` | `isVisible` | `.visible(@ShowPanel)` | OneWay |
| `*` | `opacity` | `.opacity(@FadeLevel)` | OneWay |
| `*` | `backgroundColor` | `.background(@ThemeColor)` | OneWay |
| `ProgressBar` | `progress` | `.progress(@DownloadPercent)` | OneWay |
| `ActivityIndicator` | `isRunning` | `.running(@IsLoading)` | OneWay |

### Explicit Mode Override

Any binding can override its default mode:

```mauiscript
// Force one-way on a normally two-way property
Entry
  .text(@DisplayName, mode: oneWay)

// Force two-way on a normally one-way property (rare)
Label
  .text(@EditableTitle, mode: twoWay)

// One-time binding (set once, never update)
Image
  .source(@InitialAvatar, mode: oneTime)
```

### Shorthand Binding Syntax

The shorthand `ControlType @Property` always binds to the control's **primary content property**:

| Shorthand | Equivalent | Binding Mode |
|-----------|------------|--------------|
| `Text @UserName` | `Label.text(@UserName)` | OneWay |
| `Text "Hello"` | `Label.text("Hello")` | Literal |
| `CheckBox @RememberMe` | `CheckBox.checked(@RememberMe)` | TwoWay |
| `Button @ButtonLabel` | `Button.text(@ButtonLabel)` | OneWay |
| `Image @AvatarUrl` | `Image.source(@AvatarUrl)` | OneWay |

---

## Appendix B: Expression Binding Compilation

MauiScript supports binding expressions like `@IsValid and not @IsLoading`. This section defines exactly how they compile.

### Simple Expressions

Simple boolean expressions compile to **generated computed properties** in the partial class:

```mauiscript
// MauiScript
Button "Submit"
  .enabled(@IsValid and not @IsLoading)
```

```csharp
// Generated C# (partial class)
public partial class LoginPage : ContentPage
{
    // Auto-generated computed property
    [GeneratedBinding]
    private bool CanSubmitComputed => 
        ViewModel.IsValid && !ViewModel.IsLoading;
    
    public LoginPage(LoginViewModel viewModel)
    {
        // Binding to the computed property
        submitButton.SetBinding(
            Button.IsEnabledProperty,
            new Binding(nameof(CanSubmitComputed), source: this)
        );
    }
}
```

### Why Computed Properties (Not MultiBinding)?

1. **Debuggability**: You can set breakpoints on the computed property
2. **Performance**: No converter allocation per evaluation
3. **Simplicity**: Single property change notification path
4. **Hot Reload**: Standard C# hot reload works

### Supported Expression Operators

| Operator | MauiScript | C# Output |
|----------|------------|-----------|
| Logical AND | `@A and @B` | `A && B` |
| Logical OR | `@A or @B` | `A \|\| B` |
| Logical NOT | `not @A` or `@!A` | `!A` |
| Equality | `@A == "value"` | `A == "value"` |
| Inequality | `@A != null` | `A != null` |
| Comparison | `@Count > 0` | `Count > 0` |
| Null coalesce | `@Name ?? "Guest"` | `Name ?? "Guest"` |
| Ternary | `@IsVip \| then: $Gold \| else: $Silver` | Converter-based |

### Complex Expressions with Converters

When expressions involve non-boolean results or complex transformations, they compile to inline converters:

```mauiscript
// Ternary-style binding
.color(@IsError | then: $ErrorColor | else: $TextPrimary)
```

```csharp
// Generated C#
label.SetBinding(Label.TextColorProperty, new Binding(
    nameof(ViewModel.IsError),
    converter: new TernaryConverter<bool, Color>(
        trueValue: (Color)Resources["ErrorColor"],
        falseValue: (Color)Resources["TextPrimary"]
    )
));
```

### Expression Limitations

The following are **not supported** in binding expressions (use ViewModel computed properties instead):

- Method calls: `@Items.Count()` ❌
- Complex LINQ: `@Items.Where(...)` ❌
- String interpolation: `@"Hello {@Name}"` ❌ (use spans instead)
- Arithmetic on bound values: `@Price * @Quantity` ❌

**Rationale**: These require full C# expression parsing. Keep complex logic in the ViewModel.

---

## Appendix C: Custom Components

MauiScript pages can use custom components. This section defines how components are declared and used.

### Using C# Components (Recommended)

Custom components are typically defined as standard C# classes:

```csharp
// SocialButton.cs
public class SocialButton : ContentView
{
    public static readonly BindableProperty ProviderProperty = ...;
    public static readonly BindableProperty CommandProperty = ...;
    
    public string Provider { get; set; }
    public ICommand Command { get; set; }
    
    public SocialButton()
    {
        // Build UI in C# or load from XAML
    }
}
```

```mauiscript
// Usage in MauiScript
use Components from "@app/components"

page LoginPage
  // ...
  SocialButton("google")
    .command(@GoogleLoginCommand)
```

### Component Resolution

The compiler resolves component names in this order:

1. **Built-in MAUI controls**: `Button`, `Entry`, `Label`, etc.
2. **MauiScript aliases**: `Text` → `Label`, `Stack.vertical` → `VerticalStackLayout`
3. **Imported namespaces**: Components from `use` statements
4. **Project namespace**: Any `ContentView` subclass in the project

### MauiScript-Defined Components (v2 Feature)

Future versions will support defining components in MauiScript:

```mauiscript
// social-button.maui (v2 syntax - not yet implemented)
component SocialButton
  @param provider: string
  @param command: ICommand
  
  Button
    .image("social/{@provider}.png")
    .size(48)
    .corner(24)
    .style($SocialButtonStyle)
    .command(@command)
```

**v1 Recommendation**: Define custom components in C# for now. MauiScript excels at page composition, not component internals.

### Component Constructor Arguments

When a component has constructor parameters, pass them in parentheses:

```mauiscript
// Component with constructor arguments
SocialButton("google")              // provider = "google"
  .command(@GoogleLoginCommand)

// Multiple arguments
UserCard(@user.Id, @user.Name)
  .style($CardStyle)

// Named arguments
Chart(type: "bar", animated: true)
  .data(@SalesData)
```

---

## Appendix D: Attached Properties & Third-Party Controls

This section defines how to use attached properties from MAUI and third-party libraries.

### Built-in Grid Attached Properties

Grid positioning uses shorthand modifiers:

```mauiscript
Grid["auto, *, auto", "*, *"]
  
  Text "Header"
    .row(0)
    .columnSpan(2)
  
  Text "Sidebar"
    .row(1)
    .col(0)
  
  Text "Content"
    .row(1)
    .col(1)
```

**Compilation**:
```csharp
Grid.SetRow(header, 0);
Grid.SetColumnSpan(header, 2);
```

### Generic Attached Property Syntax

For attached properties not covered by shorthand, use the `.attached()` modifier:

```mauiscript
// Syntax: .attached(OwnerType.PropertyName, value)

// Shell navigation
Button "Go to Details"
  .attached(Shell.NavBarIsVisible, false)
  .attached(Shell.TabBarIsVisible, false)

// Semantic properties (accessibility)
Image("chart.png")
  .attached(SemanticProperties.Description, "Sales chart for Q4")

// AutomationProperties
Entry #emailField
  .attached(AutomationProperties.Name, "Email Address")
  .attached(AutomationProperties.HelpText, "Enter your email")
```

### Third-Party Library Controls

Import third-party controls via `use` statements:

```mauiscript
// Import Community Toolkit
use Toolkit from "CommunityToolkit.Maui.Views"
use ToolkitBehaviors from "CommunityToolkit.Maui.Behaviors"

page MediaPage
  @viewmodel: MediaViewModel
  
  Stack.vertical
    
    // Use Toolkit control
    Toolkit:MediaElement
      .source(@VideoUrl)
      .shouldAutoPlay(true)
      .attached(Toolkit:DockLayout.Dock, "Top")
    
    // Apply Toolkit behavior
    Entry
      .text(@Email)
      .behaviors [
        ToolkitBehaviors:EmailValidationBehavior
          .validStyle($ValidInput)
          .invalidStyle($InvalidInput)
      ]
```

### Attached Property Shorthand Registration

Common attached properties can be registered as shorthand in a project config:

```json
// mauiscript.config.json
{
  "attachedPropertyShorthands": {
    "dock": "CommunityToolkit.Maui.Layouts.DockLayout.Dock",
    "semantic": "Microsoft.Maui.Controls.SemanticProperties.Description"
  }
}
```

```mauiscript
// Now usable as shorthand
Image("logo.png")
  .dock("Top")
  .semantic("Company logo")
```

---

## Appendix E: Event Handlers (Non-Command)

While commands are preferred for MVVM, sometimes you need direct event handlers for code-behind scenarios.

### Event Handler Syntax

Use `.on(EventName, HandlerMethod)` for code-behind event wiring:

```mauiscript
page InteractivePage
  @viewmodel: InteractiveViewModel
  
  Entry #searchField
    .text(@SearchQuery)
    .on(TextChanged, OnSearchTextChanged)
    .on(Focused, OnSearchFocused)
    .on(Unfocused, OnSearchUnfocused)
  
  WebView #browser
    .source(@Url)
    .on(Navigating, OnWebViewNavigating)
    .on(Navigated, OnWebViewNavigated)
  
  CollectionView
    .items(@Products)
    .on(SelectionChanged, OnProductSelected)
    .on(Scrolled, OnCollectionScrolled)
```

### Generated Code-Behind

```csharp
// InteractivePage.g.cs (generated)
public partial class InteractivePage : ContentPage
{
    public Entry searchField { get; private set; }
    
    private void InitializeComponent()
    {
        searchField = new Entry();
        searchField.TextChanged += OnSearchTextChanged;
        searchField.Focused += OnSearchFocused;
        // ...
    }
}

// InteractivePage.cs (user-written partial)
public partial class InteractivePage
{
    private void OnSearchTextChanged(object sender, TextChangedEventArgs e)
    {
        // Custom logic here
    }
    
    private void OnSearchFocused(object sender, FocusEventArgs e)
    {
        // Custom logic here
    }
}
```

### When to Use Events vs Commands

| Scenario | Recommended | Why |
|----------|-------------|-----|
| Button click → ViewModel action | `.command(@Save)` | Clean MVVM separation |
| Text validation on every keystroke | `.on(TextChanged, ...)` | Performance-critical, needs EventArgs |
| WebView navigation interception | `.on(Navigating, ...)` | Needs to set `Cancel` on args |
| Gesture with position data | `.on(PanUpdated, ...)` | Needs gesture coordinates |
| ScrollView position tracking | `.on(Scrolled, ...)` | Needs scroll offset |

### Async Event Handlers

Event handlers can be async:

```mauiscript
WebView
  .on(Navigated, OnNavigatedAsync)
```

```csharp
private async void OnNavigatedAsync(object sender, WebNavigatedEventArgs e)
{
    await ProcessNavigationAsync(e.Url);
}
```

---

## Appendix F: Naming & Scoping Rules

This section defines how element names work and how naming conflicts are resolved.

### Naming Mechanisms

MauiScript provides two equivalent ways to name elements:

```mauiscript
// Option 1: Hash suffix (preferred)
Entry #emailField
  .text(@Email)

// Option 2: Ref modifier
Entry
  .ref(emailField)
  .text(@Email)
```

**These are equivalent.** Using both on the same element is a compile error:

```mauiscript
// ❌ COMPILE ERROR: Duplicate naming
Entry #emailField
  .ref(emailField)
```

### Generated Fields

Named elements become fields in the generated partial class:

```csharp
public partial class LoginPage : ContentPage
{
    // Generated from #emailField
    public Entry emailField { get; private set; }
    
    // Generated from #passwordField  
    public Entry passwordField { get; private set; }
}
```

### Naming Rules

| Rule | Valid | Invalid |
|------|-------|---------|
| Start with letter or underscore | `#emailField`, `#_private` | `#123field` |
| Alphanumeric + underscores only | `#user_name_field` | `#user-name` |
| Case-sensitive | `#Email` ≠ `#email` | — |
| No C# reserved words | `#submitBtn` | `#class`, `#void` |
| Unique within page | One `#email` per page | Two `#email` in same page |

### Scope Rules

Names are **page-scoped** (flat namespace within a single `.maui` file):

```mauiscript
page SettingsPage

  // All names exist at page level
  Stack.vertical
    Entry #nameField
    
    Stack.horizontal
      Entry #emailField      // Same scope as #nameField
      
    when @ShowAdvanced
      Entry #advancedField   // Same scope, even inside conditional
```

### Naming in foreach Loops

Inside `foreach`, the loop variable creates a temporary scope:

```mauiscript
foreach @Items as item
  Card #itemCard              // ❌ ERROR: Would create multiple #itemCard
    Text @item.Name
```

**Solution**: Don't name elements inside loops. If you need references, use the ViewModel:

```mauiscript
foreach @Items as item
  Card
    .ref(@item.CardRef)       // Store ref in the item model
```

### Name Collision with ViewModel

If a name conflicts with a ViewModel property, the element name takes precedence in the page scope:

```mauiscript
page ProfilePage
  @viewmodel: ProfileViewModel   // Has property "Email"
  
  Entry #Email                   // This shadows ViewModel.Email in page scope
    .text(@Email)                // @Email refers to ViewModel.Email (binding)
```

**Recommendation**: Avoid naming elements the same as ViewModel properties.

---

## Appendix G: Module & Import Semantics

The `use` statement imports types and resources for use in a MauiScript file.

### Import Syntax

```mauiscript
// Import from project path
use Theme from "@app/resources"
use Components from "@app/components"

// Import from NuGet package
use Toolkit from "CommunityToolkit.Maui"
use SkiaSharp from "SkiaSharp.Views.Maui"

// Import specific types
use { MediaElement, Popup } from "CommunityToolkit.Maui"

// Aliased import
use Charts as Microcharts from "Microcharts.Maui"
```

### Path Resolution

| Path Pattern | Resolves To |
|--------------|-------------|
| `"@app/resources"` | Project's `Resources/` folder |
| `"@app/components"` | Project namespace containing components |
| `"@app/theme"` | Project's theme `.maui` file |
| `"PackageName"` | NuGet package's default namespace |
| `"PackageName.Specific"` | Specific namespace in package |

### What Imports Provide

Imports affect **compile-time resolution only**:

```mauiscript
use Toolkit from "CommunityToolkit.Maui"

page MyPage
  // Compiler now recognizes Toolkit:MediaElement
  Toolkit:MediaElement
    .source(@VideoUrl)
```

**Generated C#**:
```csharp
using CommunityToolkit.Maui.Views;

// ...
var media = new MediaElement { Source = ... };
```

### Resource Imports

Importing a resource file merges its definitions:

```mauiscript
// theme.maui
resources AppTheme
  colors
    AccentColor: #3B82F6
```

```mauiscript
// login.maui
use Theme from "@app/theme"    // Merges AppTheme resources

page LoginPage
  Button "Submit"
    .background($AccentColor)   // Available via import
```

### Import Scoping

- Imports are **file-scoped** (apply only to the current `.maui` file)
- Multiple files can import the same resources
- No "global imports" (each file declares its dependencies)

---

## Appendix H: Resource Precedence & Theme Integration

This section defines how resources are resolved and how theming works.

### Resource Lookup Order

When the compiler encounters `$ResourceName`, it searches in this order:

1. **Page-scoped resources** (defined in current `page` block)
2. **Imported resources** (from `use` statements)
3. **App-level resources** (from `resources AppTheme`)
4. **MAUI platform defaults** (system colors, etc.)

First match wins. More specific scopes shadow broader ones.

### Example Resolution

```mauiscript
// app-theme.maui
resources AppTheme
  colors
    AccentColor: #3B82F6        // App-level

// login.maui
use Theme from "@app/theme"

page LoginPage
  resources
    AccentColor: #EF4444        // Page-level (overrides app)
  
  Button "Submit"
    .background($AccentColor)   // Resolves to #EF4444 (page-level)
```

### Static vs Dynamic Resources

| Syntax | Behavior | Use Case |
|--------|----------|----------|
| `$ResourceName` | StaticResource (resolved once) | Performance-critical, won't change |
| `$$ResourceName` | DynamicResource (reactive) | Theme changes, runtime updates |

```mauiscript
// Static: resolved at page creation, never updates
.background($CardBackground)

// Dynamic: updates when theme changes
.background($$CardBackground)
```

### Theme Integration (`@dark` / `@light`)

The `@dark` block defines theme-variant overrides:

```mauiscript
resources AppTheme
  colors
    TextPrimary: #1F2937
    Background: #FFFFFF
    
    @dark
      TextPrimary: #F9FAFB
      Background: #111827
```

**Compilation**: Generates resources with `AppThemeBinding`:

```csharp
// Generated resource dictionary
Resources.Add("TextPrimary", new AppThemeBinding
{
    Light = Color.FromHex("#1F2937"),
    Dark = Color.FromHex("#F9FAFB")
});
```

### Automatic Theme Reactivity

When using `$$` (dynamic), theme changes automatically propagate:

```mauiscript
Text "Hello"
  .color($$TextPrimary)    // Auto-updates on theme change
```

When using `$` (static), the initial theme value is baked in:

```mauiscript
Text "Hello"
  .color($TextPrimary)     // Uses value at page creation time
```

**Recommendation**: Use `$$` for colors and backgrounds that should respond to theme changes.

---

## Appendix I: Comments & Documentation

### Comment Syntax

MauiScript supports single-line comments only:

```mauiscript
// This is a comment
page LoginPage
  @viewmodel: LoginViewModel    // Inline comment
  
  // Section: Header
  Stack.vertical
    Text "Welcome"              // Welcome message
```

### No Block Comments

Block comments (`/* */`) are **not supported** to keep the grammar simple and avoid nesting ambiguities.

**Workaround**: Use multiple single-line comments:

```mauiscript
// ═══════════════════════════════════════════════════════
// FORM SECTION
// This section contains the login form inputs
// ═══════════════════════════════════════════════════════
```

### Documentation Comments (Future)

Future versions may support doc comments for component documentation:

```mauiscript
/// <summary>
/// Login page with email/password authentication
/// </summary>
/// <viewmodel>LoginViewModel</viewmodel>
page LoginPage
```

**v1**: Not implemented. Use external documentation.

---

## Appendix J: Compile-Time Error Examples

This section shows example error messages to help developers understand what went wrong.

### Missing Resource

```mauiscript
Button "Submit"
  .background($PrimaryButtonColor)   // Resource doesn't exist
```

```
error MS1001: Resource 'PrimaryButtonColor' not found.
  --> login.maui:45:16
   |
45 |   .background($PrimaryButtonColor)
   |                ^^^^^^^^^^^^^^^^^^
   |
   = hint: Did you mean '$PrimaryButton' (defined in app-theme.maui:23)?
   = hint: Available colors: $AccentColor, $ErrorColor, $TextPrimary
```

### Missing ViewModel Property

```mauiscript
Entry
  .text(@UserEmail)    // ViewModel has 'Email', not 'UserEmail'
```

```
error MS1002: Property 'UserEmail' not found on LoginViewModel.
  --> login.maui:32:9
   |
32 |   .text(@UserEmail)
   |         ^^^^^^^^^^
   |
   = hint: Did you mean '@Email'?
   = hint: Available properties: Email, Password, RememberMe, IsLoading
```

### Type Mismatch

```mauiscript
Button "Submit"
  .enabled(@UserName)   // UserName is string, enabled expects bool
```

```
error MS1003: Type mismatch in binding.
  --> login.maui:38:12
   |
38 |   .enabled(@UserName)
   |            ^^^^^^^^^
   |
   = expected: bool (for IsEnabled property)
   = found: string (UserName property type)
   = hint: Use a converter: .enabled(@UserName | isNotEmpty)
```

### Duplicate Name

```mauiscript
Entry #emailField
  .text(@Email)

Entry #emailField        // Duplicate!
  .text(@ConfirmEmail)
```

```
error MS1004: Duplicate element name 'emailField'.
  --> login.maui:48:7
   |
48 | Entry #emailField
   |       ^^^^^^^^^^^
   |
   = note: First defined at login.maui:42
   = hint: Element names must be unique within a page
```

### Invalid Binding Expression

```mauiscript
Button "Submit"
  .enabled(@Items.Count() > 0)   // Method calls not allowed
```

```
error MS1005: Invalid binding expression.
  --> login.maui:55:12
   |
55 |   .enabled(@Items.Count() > 0)
   |            ^^^^^^^^^^^^^^^^^^
   |
   = Method calls are not supported in binding expressions
   = hint: Create a computed property in your ViewModel:
   |   public bool HasItems => Items.Count > 0;
   = then use: .enabled(@HasItems)
```

### Unknown Control

```mauiscript
TextEdit           // Not a MAUI control
  .text(@Notes)
```

```
error MS1006: Unknown control type 'TextEdit'.
  --> login.maui:28:1
   |
28 | TextEdit
   | ^^^^^^^^
   |
   = hint: Did you mean 'Editor' (multi-line text input)?
   = hint: Or 'Entry' (single-line text input)?
```

---

## Appendix K: Additional Page Examples

These additional examples stress-test the syntax across different page types.

### Settings Page

```mauiscript
// settings.maui
use Theme from "@app/resources"

page SettingsPage
  @viewmodel: SettingsViewModel
  @title: "Settings"
  
  Scroll > Stack.vertical.spacing(0)
    
    // ═══════════════════════════════════════════════════════
    // ACCOUNT SECTION
    // ═══════════════════════════════════════════════════════
    
    SectionHeader "Account"
    
    SettingsRow
      .icon("person")
      .title("Profile")
      .subtitle(@UserEmail)
      .tap(@NavigateToProfileCommand)
    
    SettingsRow
      .icon("lock")
      .title("Change Password")
      .tap(@NavigateToPasswordCommand)
    
    SettingsRow
      .icon("bell")
      .title("Notifications")
      .accessory(
        Switch.toggled(@NotificationsEnabled)
      )
    
    // ═══════════════════════════════════════════════════════
    // APPEARANCE SECTION
    // ═══════════════════════════════════════════════════════
    
    SectionHeader "Appearance"
    
    SettingsRow
      .icon("moon")
      .title("Dark Mode")
      .accessory(
        Switch.toggled(@DarkModeEnabled)
      )
    
    SettingsRow
      .icon("text")
      .title("Text Size")
      .subtitle(@TextSizeLabel)
      .accessory(
        Slider
          .value(@TextSize)
          .min(12)
          .max(24)
          .width(120)
      )
    
    SettingsRow
      .icon("palette")
      .title("Accent Color")
      .accessory(
        Stack.horizontal.spacing(8)
          foreach @AccentColors as color
            Circle
              .size(24)
              .fill(@color.Value)
              .border(2, @SelectedAccent == @color ? $TextPrimary : transparent)
              .tap(@SelectAccentCommand, param: @color)
      )
    
    // ═══════════════════════════════════════════════════════
    // DATA SECTION
    // ═══════════════════════════════════════════════════════
    
    SectionHeader "Data & Storage"
    
    SettingsRow
      .icon("cloud")
      .title("Sync")
      .subtitle(@LastSyncLabel)
      .accessory(
        when @IsSyncing
          ActivityIndicator.size(small)
        else
          Button "Sync Now"
            .style($SmallButton)
            .command(@SyncCommand)
      )
    
    SettingsRow
      .icon("trash")
      .title("Clear Cache")
      .subtitle(@CacheSizeLabel)
      .tap(@ClearCacheCommand)
      .destructive(true)
    
    // ═══════════════════════════════════════════════════════
    // ABOUT SECTION
    // ═══════════════════════════════════════════════════════
    
    SectionHeader "About"
    
    SettingsRow
      .icon("info")
      .title("Version")
      .subtitle(@AppVersion)
    
    SettingsRow
      .icon("document")
      .title("Terms of Service")
      .tap(@OpenTermsCommand)
    
    SettingsRow
      .icon("shield")
      .title("Privacy Policy")
      .tap(@OpenPrivacyCommand)
    
    // ═══════════════════════════════════════════════════════
    // DANGER ZONE
    // ═══════════════════════════════════════════════════════
    
    SectionHeader "Danger Zone"
      .color($ErrorColor)
    
    SettingsRow
      .icon("logout")
      .title("Sign Out")
      .tap(@SignOutCommand)
      .destructive(true)
    
    SettingsRow
      .icon("trash")
      .title("Delete Account")
      .subtitle("This cannot be undone")
      .tap(@DeleteAccountCommand)
      .destructive(true)
    
    // Footer
    Text "Made with ❤️ by MauiScript"
      .font(size: 12)
      .color($TextTertiary)
      .center
      .m(top: 32, bottom: 16)
```

### List/Detail Page (Master-Detail)

```mauiscript
// products.maui
use Theme from "@app/resources"

page ProductsPage
  @viewmodel: ProductsViewModel
  @title: "Products"
  
  // Pull-to-refresh wrapper
  RefreshView(@RefreshCommand, @IsRefreshing) > Stack.vertical
    
    // Search bar
    SearchBar #searchBar
      .text(@SearchQuery)
      .placeholder("Search products...")
      .on(TextChanged, OnSearchTextChanged)
      .m(horizontal: 16, vertical: 8)
    
    // Filter chips
    Scroll.horizontal.showsIndicators(false)
      Stack.horizontal.spacing(8).p(horizontal: 16)
        
        foreach @Categories as category
          Chip
            .text(@category.Name)
            .selected(@SelectedCategory == @category)
            .tap(@SelectCategoryCommand, param: @category)
    
    // Results count
    Text [
      span @FilteredCount { weight: bold }
      span " products found"
    ]
      .font(size: 14)
      .color($TextSecondary)
      .m(horizontal: 16, vertical: 8)
      .visible(@FilteredCount > 0)
    
    // Product grid
    Collection(@FilteredProducts)
      .layout(grid, columns: 2, spacing: 16)
      .p(16)
      .emptyView(
        EmptyState
          .icon("box")
          .title("No products found")
          .subtitle("Try adjusting your search or filters")
          .action("Clear Filters", @ClearFiltersCommand)
      )
      .template(product =>
        Card
          .tap(@SelectProductCommand, param: @product)
          
          Stack.vertical.spacing(8)
            
            Image(@product.ImageUrl)
              .aspect(aspectFill)
              .height(120)
              .corner(8)
            
            Text @product.Name
              .font(size: 14, weight: semibold)
              .maxLines(2)
            
            Stack.horizontal.justify(spaceBetween)
              Text @product.Price
                .font(size: 16, weight: bold)
                .color($AccentColor)
              
              when @product.OriginalPrice > @product.Price
                Text @product.OriginalPrice
                  .font(size: 12)
                  .color($TextTertiary)
                  .strikethrough
            
            Stack.horizontal.spacing(4)
              Icon("star", size: 14, color: #FCD34D)
              Text @product.Rating
                .font(size: 12)
              Text "({@product.ReviewCount})"
                .font(size: 12)
                .color($TextTertiary)
      )
    
    // Loading overlay
    when @IsLoading and not @IsRefreshing
      AbsoluteLayout
        .fill
        .background(#00000040)
        
        ActivityIndicator
          .center
          .color(White)
          .running(true)
```

### Dashboard Page

```mauiscript
// dashboard.maui
use Theme from "@app/resources"
use Charts from "Microcharts.Maui"

page DashboardPage
  @viewmodel: DashboardViewModel
  @title: "Dashboard"
  
  Scroll > Stack.vertical.spacing(24).p(16)
    
    // ═══════════════════════════════════════════════════════
    // GREETING
    // ═══════════════════════════════════════════════════════
    
    Stack.horizontal.justify(spaceBetween).align(center)
      Stack.vertical.spacing(4)
        Text @GreetingText
          .font(size: 14)
          .color($TextSecondary)
        Text "Welcome back, {@UserName}!"
          .font(size: 24, weight: bold)
      
      Image(@UserAvatar)
        .size(48)
        .corner(24)
        .tap(@NavigateToProfileCommand)
    
    // ═══════════════════════════════════════════════════════
    // STATS CARDS
    // ═══════════════════════════════════════════════════════
    
    Stack.horizontal.spacing(16)
      
      StatCard
        .title("Revenue")
        .value(@RevenueFormatted)
        .change(@RevenueChange)
        .icon("dollar")
        .color($SuccessColor)
        .flex(1)
      
      StatCard
        .title("Orders")
        .value(@OrderCount)
        .change(@OrderChange)
        .icon("cart")
        .color($AccentColor)
        .flex(1)
    
    Stack.horizontal.spacing(16)
      
      StatCard
        .title("Customers")
        .value(@CustomerCount)
        .change(@CustomerChange)
        .icon("people")
        .color($WarningColor)
        .flex(1)
      
      StatCard
        .title("Conversion")
        .value(@ConversionRate)
        .change(@ConversionChange)
        .icon("trending")
        .color($InfoColor)
        .flex(1)
    
    // ═══════════════════════════════════════════════════════
    // REVENUE CHART
    // ═══════════════════════════════════════════════════════
    
    Card
      Stack.vertical.spacing(16)
        
        Stack.horizontal.justify(spaceBetween).align(center)
          Text "Revenue Overview"
            .font(size: 18, weight: semibold)
          
          Picker
            .items(@ChartPeriods)
            .selectedIndex(@SelectedPeriodIndex)
            .width(120)
        
        Charts:LineChart
          .entries(@RevenueChartData)
          .lineColor($AccentColor)
          .height(200)
          .animated(true)
    
    // ═══════════════════════════════════════════════════════
    // RECENT ORDERS
    // ═══════════════════════════════════════════════════════
    
    Card
      Stack.vertical.spacing(16)
        
        Stack.horizontal.justify(spaceBetween).align(center)
          Text "Recent Orders"
            .font(size: 18, weight: semibold)
          
          Text "View All"
            .color($AccentColor)
            .tap(@ViewAllOrdersCommand)
        
        foreach @RecentOrders as order
          Stack.horizontal.spacing(12).align(center)
            
            Image(@order.CustomerAvatar)
              .size(40)
              .corner(20)
            
            Stack.vertical.spacing(2).flex(1)
              Text @order.CustomerName
                .font(size: 14, weight: medium)
              Text @order.OrderNumber
                .font(size: 12)
                .color($TextSecondary)
            
            Stack.vertical.spacing(2).align(end)
              Text @order.TotalFormatted
                .font(size: 14, weight: semibold)
              StatusBadge(@order.Status)
          
          when @order != @RecentOrders.Last
            Divider.m(vertical: 8)
    
    // ═══════════════════════════════════════════════════════
    // QUICK ACTIONS
    // ═══════════════════════════════════════════════════════
    
    Text "Quick Actions"
      .font(size: 18, weight: semibold)
    
    Stack.horizontal.spacing(12)
      
      QuickActionButton
        .icon("plus")
        .label("New Order")
        .command(@NewOrderCommand)
        .flex(1)
      
      QuickActionButton
        .icon("scan")
        .label("Scan Item")
        .command(@ScanItemCommand)
        .flex(1)
      
      QuickActionButton
        .icon("report")
        .label("Reports")
        .command(@ViewReportsCommand)
        .flex(1)
      
      QuickActionButton
        .icon("settings")
        .label("Settings")
        .command(@OpenSettingsCommand)
        .flex(1)
```

---

*End of Appendices*

---

## Appendix L: .NET MAUI 10 Compatibility

This appendix documents how MauiScript aligns with .NET MAUI 10 changes, deprecations, and new features.

### Deprecated Controls (Not Supported in MauiScript)

The following controls are deprecated in .NET MAUI 10 and **not available** in MauiScript:

| Deprecated Control | MauiScript Alternative |
|--------------------|----------------------|
| `ListView` | `Collection` (CollectionView) |
| `TableView` | `Collection` with sections |
| `EntryCell` | Custom component in Collection |
| `ImageCell` | Custom component in Collection |
| `SwitchCell` | Custom component in Collection |
| `TextCell` | Custom component in Collection |
| `ViewCell` | Custom template in Collection |

```mauiscript
// ❌ NOT SUPPORTED (deprecated)
ListView
  .items(@Items)

// ✅ USE INSTEAD
Collection(@Items)
  .layout(vertical)
  .template(item => ...)
```

### Removed Controls

| Removed | Replacement |
|---------|-------------|
| `Accelerator` | `KeyboardAccelerator` |
| `ClickGestureRecognizer` | `.tap()` modifier (TapGestureRecognizer) |
| `Compatibility.Layout` | Use standard layouts |

### Deprecated Methods & Properties

MauiScript generates code using the new async method names:

| Deprecated | Generated As |
|------------|--------------|
| `FadeTo` | `FadeToAsync` |
| `ScaleTo` | `ScaleToAsync` |
| `TranslateTo` | `TranslateToAsync` |
| `RotateTo` | `RotateToAsync` |
| `DisplayAlert` | `DisplayAlertAsync` |
| `DisplayActionSheet` | `DisplayActionSheetAsync` |
| `Page.IsBusy` | Uses `ActivityIndicator` (our `.loading()` pattern) |

### SafeAreaEdges (New in .NET MAUI 10)

MauiScript supports the new `SafeAreaEdges` enum:

```mauiscript
// SafeArea options
page MyPage
  .safeArea(none)           // Edge-to-edge content
  .safeArea(softInput)      // Pad for keyboard only
  .safeArea(container)      // Flow under keyboard, respect bars/notch
  .safeArea(default)        // Platform default (recommended)
  .safeArea(all)            // Obey all safe area insets

// Applied to layouts
Stack.vertical
  .safeArea(container)
  
Scroll
  .safeArea(none)           // Edge-to-edge scrolling
  
Grid
  .safeArea(softInput)      // Respects keyboard but not bars
```

**Generated C#**:
```csharp
page.SafeAreaEdges = SafeAreaEdges.Container;
```

### Nullable DatePicker and TimePicker

.NET MAUI 10 makes `DatePicker.Date` and `TimePicker.Time` nullable. MauiScript handles this automatically:

```mauiscript
// Nullable date binding
DatePicker
  .date(@BirthDate)           // BirthDate can be DateTime?
  .minDate(@MinAllowedDate)   // Also nullable
  .maxDate(@MaxAllowedDate)

// Clear button support
DatePicker
  .date(@SelectedDate)
  .clearable(true)            // Adds clear button
```

### New Control Properties

#### Switch.OffColor

```mauiscript
Switch
  .toggled(@DarkMode)
  .onColor($SuccessColor)
  .offColor($ErrorColor)       // New in .NET 10
```

#### SearchBar Enhancements

```mauiscript
SearchBar
  .text(@Query)
  .searchIconColor($AccentColor)  // New in .NET 10
  .returnType(search)             // New in .NET 10 (default: search)
```

#### RefreshView.IsRefreshEnabled

```mauiscript
RefreshView(@RefreshCommand, @IsRefreshing)
  .refreshEnabled(@CanRefresh)    // New: distinct from isEnabled
  
  // Content remains interactive even when refresh is disabled
  Stack.vertical
    Entry.text(@Username)
    Entry.text(@Password)
    Button "Login".command(@LoginCommand)
```

#### Picker Open/Close

```mauiscript
Picker #countryPicker
  .items(@Countries)
  .selectedIndex(@SelectedCountryIndex)

Button "Select Country"
  .tap(open: #countryPicker)      // Programmatically open picker
```

### HybridWebView Enhancements

```mauiscript
HybridWebView
  .source("index.html")
  .on(WebViewInitializing, OnInitializing)   // New event
  .on(WebViewInitialized, OnInitialized)     // New event
  .on(WebResourceRequested, OnResourceRequested)  // Intercept requests
```

### MessagingCenter Replacement

`MessagingCenter` is now internal in .NET MAUI 10. MauiScript does not generate any `MessagingCenter` code. Use `WeakReferenceMessenger` from CommunityToolkit.Mvvm:

```mauiscript
// In ViewModel (C#)
use CommunityToolkit.Mvvm.Messaging

// MauiScript doesn't handle messaging - it's ViewModel responsibility
```

### Animation Syntax (Async by Default)

MauiScript's `.animate()` modifier generates the new async animation methods:

```mauiscript
Image("logo.png")
  .animate(fade, duration: 300)
  .animate(scale, from: 0.8, to: 1.0)
```

**Generated C# (.NET 10)**:
```csharp
await image.FadeToAsync(1.0, 300);  // Note: Async suffix
await image.ScaleToAsync(1.0, 300);
```

### XAML Source Generator Alignment

MauiScript's transpilation strategy aligns with .NET MAUI 10's new XAML source generator:

- Both generate strongly-typed C# at compile time
- Both provide better IntelliSense and compile-time error checking
- MauiScript generates C# directly (not XAML), avoiding the XAML parser entirely

### Global Namespace Benefits

.NET MAUI 10's global XML namespace feature reduces XAML boilerplate. MauiScript already solved this problem differently:

| XAML 10 Approach | MauiScript Approach |
|------------------|---------------------|
| Global xmlns definitions | `use` imports at file top |
| `http://schemas.microsoft.com/dotnet/maui/global` | `use Theme from "@app/resources"` |
| Assembly-level `XmlnsDefinition` | Automatic namespace resolution |

Both approaches eliminate repetitive namespace declarations, but MauiScript's module system is more explicit and tooling-friendly.

### Secondary Toolbar Items (iOS/macOS)

.NET MAUI 10 adds proper secondary toolbar support on iOS:

```mauiscript
page MyPage
  @title: "Document"
  
  toolbar
    ToolbarItem "Save"
      .order(primary)
      .icon("save")
      .command(@SaveCommand)
    
    ToolbarItem "Share"
      .order(secondary)          // Goes to overflow menu on iOS
      .priority(0)
      .command(@ShareCommand)
    
    ToolbarItem "Delete"
      .order(secondary)
      .priority(1)
      .command(@DeleteCommand)
```

### Diagnostics Integration

MauiScript can optionally generate diagnostics hooks for .NET MAUI 10's new metrics:

```mauiscript
// Enable layout diagnostics in debug builds
page MyPage
  @diagnostics: layout           // Instruments Measure/Arrange calls

// Generated C# includes ActivitySource instrumentation
```

### Version Targeting

MauiScript projects specify the target .NET version in the project file:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net10.0-android;net10.0-ios;net10.0-maccatalyst</TargetFramework>
    <MauiScriptVersion>0.1.2</MauiScriptVersion>
  </PropertyGroup>
  
  <ItemGroup>
    <PackageReference Include="MauiScript.Compiler" Version="0.1.2" />
  </ItemGroup>
</Project>
```

The compiler automatically:
- Uses `.NET 10 APIs when targeting `net10.0-*`
- Generates deprecation warnings for .NET 9 patterns
- Enables new features only on supported platforms

---

*MauiScript v0.1.2 — Updated for .NET MAUI 10 compatibility*
