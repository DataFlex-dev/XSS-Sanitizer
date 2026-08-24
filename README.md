# DataFlex XSS Sanitizer Library

A DataFlex library for sanitizing HTML input to prevent cross-site scripting (XSS) vulnerabilities. This library provides a configurable sanitizer class and a demo dashboard for testing.

## Features

- **Configurable Allowed/Forbidden Tags:** Specify which HTML tags are permitted or blocked.
- **Configurable Allowed/Forbidden Attributes:** Control which tag attributes are allowed or forbidden.
- **Attribute Value Validation:** Validates risky attribute contents such as `href`, `src`, and inline `style`.
- **Void and Self-Closing Tag Support:** Handles HTML void tags and self-closing tags.
- **Sanitization Modes:** Choose between allow-list or block-list modes for tags and attributes.
- **Automatic Escaping:** Escapes unsafe content and tags not matching your configuration.
- **Demo Dashboard:** Interactive web dashboard for testing and visualizing sanitization.

## Getting Started

### 1. Include the Sanitizer

Add the sanitizer object to your project:

```dataflex
Use oXssSanitizerStandard.pkg // Standard configuration
```

This sets up a global sanitizer object (`ghoSanitizer`) with a safe default configuration.

For Markdown source, use `oXssSanitizerMarkdown.pkg`. It exposes
`ghoMarkdownSanitizer` and removes raw HTML plus non-HTTP(S) Markdown link
destinations while preserving fenced code blocks. Call `SanitizeMarkdown` before
storing the source and again before returning it to a renderer. Its inherited
`Sanitize` function remains available for sanitizing generated HTML separately.

### 2. Sanitize HTML Input

Call the `Sanitize` function to clean user input:

```dataflex
String sInput sSanitized
Move "<div onclick='evil()'>Hello <img src='x.jpg'></div>" to sInput
Get Sanitize of ghoSanitizer sInput to sSanitized
```

### 3. Configure Allowed Tags and Attributes

You can customize allowed tags and attributes:

```dataflex
Send SetAllowedTags of ghoSanitizer "div,span,a,img"
Send SetAllowedAttributes of ghoSanitizer "href,src,alt"
Send SetUrlAttributes of ghoSanitizer "href,src"
Send SetAllowedUrlSchemes of ghoSanitizer "http,https,mailto"
Send SetVoidTags of ghoSanitizer "img,br,hr"
Set peSanitizeMode of ghoSanitizer to SANITIZE_MODE_ALLOWED // or SANITIZE_MODE_BLOCKED
```

Allowed attributes are now checked by both name and value. For URL-bearing attributes such as `href` and `src`, dangerous protocols like `javascript:` are stripped even when the attribute itself is allowed.

## Demo Dashboard

A demo dashboard is included (`Demo\AppSrc\Dashboard.wo`) for interactive testing. It lets you input HTML, sanitize it, and view the output and rendered result.

## API Reference

- `Sanitize(String sInput)`: Returns sanitized HTML.
- `SanitizeMarkdown(String sInput)`: Returns Markdown source with raw HTML and unsafe link destinations removed.
- `SetAllowedTags(String sTags)`: Comma-separated list of allowed tags.
- `SetForbiddenTags(String sTags)`: Comma-separated list of forbidden tags.
- `SetAllowedAttributes(String sAttributes)`: Comma-separated list of allowed attributes.
- `SetForbiddenAttributes(String sAttributes)`: Comma-separated list of forbidden attributes.
- `SetUrlAttributes(String sAttributes)`: Attributes whose values should be treated as URLs and checked against an allowed scheme list.
- `SetAllowedUrlSchemes(String sSchemes)`: Comma-separated list of allowed URL schemes for URL-bearing attributes.
- `SetVoidTags(String sVoidTags)`: Comma-separated list of void/self-closing tags.
- `peSanitizeMode`: Property to set allow-list or block-list mode.

## Example

```dataflex
Use oXssSanitizerStandard.pkg

Object oWebButton1 is a cWebButton
    Procedure OnClick
        String sInput sSanitized
        WebGet psValue of oWfInput to sInput
        Get Sanitize of ghoSanitizer sInput to sSanitized
        WebSet psValue of oWfOutput to sSanitized
        Send UpdateHtml of oWebHtmlBox1 sSanitized
    End_Procedure
End_Object
```

## License

MIT License (or specify your license here)

---

*For questions or contributions, please open an issue or pull request.*

## Implementation

See [`cXssSanitizer`](Library/AppSrc/cXssSanitizer.pkg) for full implementation details.

## General Information

| Product  | Version           |
| -------- | ----------------- |
| DataFlex | 19.1 - 25.0       |
