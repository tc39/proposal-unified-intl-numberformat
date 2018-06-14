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

## I. Spec Cleanup

Certain sections of the spec have been refactored with the following objectives:

- Fix https://github.com/tc39/ecma402/issues/238 (currency long name has dependency on plural form, and the currency long name pattern has dependency on currencyWidth).
- Move pattern resolution out of the constructor to keep all internal fields of NumberFormat locale-agnostic, making it easier to reason about behavior in the format method.

## II. Measure Units

Units of measurement can be formatted as follows:

```javascript
(9.81).toLocaleString("en-US", {
    style: "unit",
    unit: "acceleration-meter-per-second-squared",
    unitDisplay: "short"
});
// ==> "9.81 m/s²"
```

The syntax was discussed in #3.

- `style` receives the string value "unit"
- `unit` receives a string measure unit identifier, defined in [UTS #35](http://unicode.org/reports/tr35/tr35-general.html#Unit_Elements).  See also the [full list of unit identifiers](https://unicode.org/repos/cldr/tags/latest/common/validity/unit.xml).
- `unitDisplay`, named after the corresponding setting for currencies, `currencyDisplay`, takes either "narrow", "short", or "long".

## III. Scientific and Compact Notation

Scientific and compact notation are represented by the new option `notation` and can be formatted as follows:

```javascript
(987654321).toLocaleString("en-US", {
    notation: "scientific"
});
// ==> 9.877E8

(987654321).toLocaleString("en-US", {
    notation: "compact",
    notationDisplay: "long"
});
// ==> 987.7 million
```

The syntax was discussed in #5.

## IV. Sign Display
