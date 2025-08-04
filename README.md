# DataFlex XSS Sanitizer Library

A DataFlex library for sanitizing HTML input to prevent cross-site scripting (XSS) vulnerabilities. This library provides a configurable sanitizer class and a demo dashboard for testing.

## Features

- **Configurable Allowed/Forbidden Tags:** Specify which HTML tags are permitted or blocked.
- **Configurable Allowed/Forbidden Attributes:** Control which tag attributes are allowed or forbidden.
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
Send SetVoidTags of ghoSanitizer "img,br,hr"
Set peSanitizeMode of ghoSanitizer to SANITIZE_MODE_ALLOWED // or SANITIZE_MODE_BLOCKED
```

## Demo Dashboard

A demo dashboard is included (`Demo\AppSrc\Dashboard.wo`) for interactive testing. It lets you input HTML, sanitize it, and view the output and rendered result.

## API Reference

- `Sanitize(String sInput)`: Returns sanitized HTML.
- `SetAllowedTags(String sTags)`: Comma-separated list of allowed tags.
- `SetForbiddenTags(String sTags)`: Comma-separated list of forbidden tags.
- `SetAllowedAttributes(String sAttributes)`: Comma-separated list of allowed attributes.
- `SetForbiddenAttributes(String sAttributes)`: Comma-separated list of forbidden attributes.
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
| DataFlex | 23.0, 24.0, 25.0  |
