# URL sanitization

URL-valued attributes are checked by `cXssSanitizer` after the configured
attribute allow-list or block-list has been applied. This also protects callers
that explicitly enable hyperlinks.

## Policy

- The default URL attributes are `href,src,action,formaction,poster,xlink:href`.
- The default permitted explicit schemes are `http,https,mailto,tel`.
- Relative paths, fragments and query-only URLs remain supported.
- Unapproved schemes such as `javascript:`, `vbscript:` and `data:` cause
  the entire attribute to be removed; the surrounding allowed tag/text remains.
- Matching is case-insensitive and accounts for literal spaces, tabs, CR and LF.
- Entity references before an established scheme/path/query/fragment boundary
  are rejected conservatively. This closes encoded-colon, encoded-letter and
  encoded-whitespace bypasses without a partial HTML entity decoder.
- Entity references in normal queries and paths remain intact, for example
  `https://example.invalid/?a=1&amp;b=2` and `./details?a=1&amp;b=2`.
  A relative name beginning `a&amp;b` is deliberately rejected; use a clear
  relative path such as `./a&amp;b` when that is the intended URL.
- Single-quoted, double-quoted and unquoted URL values are checked.

The standard object still permits only `class,style,src` attributes by default.
Enabling `href` is an application choice. URL validation does not enable it.

## Configuration

The new `pasUrlAttributes` and `pasAllowedUrlSchemes` string-array properties
have companion setters, following the existing library convention:

```dataflex
// Example (constructed): the default URL policy, configured explicitly.
Send SetUrlAttributes of ghoSanitizer "href,src,action,formaction,poster,xlink:href"
Send SetAllowedUrlSchemes of ghoSanitizer "http,https,mailto,tel"
```

Each setter also accepts an empty string followed by a string-array argument,
as the existing tag/attribute setters do. Omitting a URL attribute from the
list disables this URL check for that attribute. Additional schemes should
only be allowed when the application explicitly requires and trusts them.

## Regression test

From the repository root, using DataFlex 25.0 x64:

```powershell
.\Tests\Run-UrlSanitizerRegression.ps1 -CompilerPath 'C:\ProjectsDF\DataFlex 25.0\Bin64\DfCompConsole.exe'
```

Adjust the compiler path to your installation. The script uses the library's
25.0 workspace by default. For another supplied version, provide its matching
workspace with `-WorkspacePath` and use the matching compiler. It copies the
two library packages and the test program to a new temporary build directory,
compiles them, and runs the tests without a WebApp or database. It prints the
results path and raises an error if compilation, execution or any check fails.
An optional `-OutputDirectory` must name a directory that does not yet exist.

The 47 checks include explicitly enabled hyperlinks, the three reported
entity bypasses, further encodings/whitespace variants, all default URL
attributes, safe schemes/relative URLs, both sanitization modes and existing
tag/event-attribute filtering. The standard hyperlink setting is also tested.

## Implementation references

Official DataFlex documentation consulted through `dae-help-mcp`:

- `docs/LanguageReference/Pos_Function.md`: substring positions, zero when absent.
- `docs/LanguageReference/Mid_Function.md`: string, length, 1-based position.
- `docs/LanguageReference/Character_Function.md`: tab/CR/LF characters.
- `docs/LanguageReference/Function_return_Command.md`: function result/early return.
- `docs/LanguageReference/Procedure_Command.md`: typed procedure parameters.
- `docs/Tools/Using_the_Console_Compiler.md` and
  `docs/Tools/Compiler_Options.md`: matching-version console compilation.

The HTML decoding behavior is specified in the
[HTML character-reference syntax](https://html.spec.whatwg.org/multipage/syntax.html#character-references)
and [named references](https://html.spec.whatwg.org/multipage/named-characters.html).

This change concerns URL attributes. It is not an audit of all HTML/CSS
parsing behavior or an instruction to enable additional tags or attributes.