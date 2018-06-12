Intl.NumberFormat Unified API Proposal
======================================

## Background / Motivation

There are many requests for adding number-formatting-related features to ECMA 402. These include:

- [Expose narrow currency symbol](https://github.com/tc39/ecma402/issues/200)
- [Add currency accounting format](https://github.com/tc39/ecma402/issues/186)
- [Add scientific notation](https://github.com/tc39/ecma402/issues/164)
- [Add option to force sign](https://github.com/tc39/ecma402/issues/163)
- [Support algorithmig numbering systems](https://github.com/tc39/ecma402/issues/95)
- [Simpler range patterns for numbers](https://github.com/tc39/ecma402/issues/91)
- [Add compact decimal notation](https://github.com/tc39/ecma402/issues/37)
- [Add measure unit formatting](https://github.com/tc39/ecma402/issues/32)

These features are important to both end users and to Google.  Since most of these features require carrying along large amounts of locale data for proper i18n support, exposing these features via a JavaScript API reduces bandwidth and lowers the barrier to entry for i18n best practices.

Rather than complicate `Intl` with more constructors with heavilly overlapping functionality, this proposal is to restructure the spec of `Intl.NumberFormat` to make it more easilly support additional features in a "unified" way.

Additional background: [Unified API for number formatting](https://github.com/tc39/ecma402/issues/215)

## Spec Cleanup: Options Orthogonality

The NumberFormat specification as currently written entangles certain locale details with options resolution.  I propose to add an abstraction layer that maps user-specified options to a locale-agnostic set of orthogonal resolved options ("ResolvedOptions"), followed by a final step that maps those locale-agnostic set of options to the locale-sensitive number formatting details ("LocalizedOptions").

The spec cleanup into ResolvedOptions/LocalizedOptions should have no effect on behavior.

### Step 1: ResolvedOptions

### Step 2: LocalizedOptions

## Measure Units

## Scientific and Compact Notation

## Sign Display
