# Golden Liquid

A test suite for [Liquid](https://shopify.github.io/liquid/), the safe, customer-facing template language for flexible web apps.

Intended for situations where template authors are untrusted and, perhaps, not software developers, Liquid is deliberately both restrictive and forgiving. It is restrictive in that Liquid expressions are somewhat limited compared to those found in other template languages, and forgiving in the way it automatically coerces data types and handles undefined variables.

The tests defined in `golden_liquid.json` attempt to cover many of Liquid's limitations, as well as the behavior of all standard tags and filters.

- [Standard Liquid](#standard-liquid)
- [Test File Schema](#test-file-schema)
- [More Information](#more-information)
- [Results Summary](#results-summary)

## Standard Liquid

For our purposes, "standard" Liquid is the one described [here](https://shopify.github.io/liquid/), with [Ruby Liquid](https://github.com/Shopify/liquid) being the reference implementation. Not to be confused with the extended variation of Liquid that is used for Shopify stores.

All tests pass with Liquid version 5.4.0 and Ruby 3. Some `round` filter test cases fail with Ruby 2.7 due to some changes with Ruby's BigDecimal library (see issue [#1590](https://github.com/Shopify/liquid/issues/1590)). If you have Ruby installed, you can run the test suite against the reference implementation by cloning this repository and running the following commands.

```
cd liquid
bundle install
bundle exec ruby golden_liquid.rb
```

## Test File Schema

In `golden_liquid.json`, tests are grouped. Each group has a name and an array of test cases. Including a version number, `golden_liquid.json` looks like this.

```json
{
    "version": "0.14.0",
    "test_groups": [
        {
            "name": "liquid.golden.abs_filter",
            "tests": [
                {
                    "name": "negative float",
                    "template": "{{ -5.4 | abs }}",
                    "want": "5.4",
                    "context": {},
                    "partials": {},
                    "error": false,
                    "strict": false
                },
                .
                .
            ]
        },
        .
        .
    ]
}
```

`golden_liquid.yaml` is the same test suite in YAML format.

```yaml
version: 0.14.0
test_groups:
  - name: liquid.golden.abs_filter
    tests:
      - name: negative float
        template: "{{ -5.4 | abs }}"
        want: "5.4"
        context: {}
        partials: {}
        error: false
        strict: false
```

For each test case:

**`name`** is a descriptive name for the test case. Together with the group name, it
should uniquely identify the test case.

**`template`** is the Liquid template source as a string.

**`want`** is the expected result of rendering the template with the associated
context.

**`context`** is JSON object mapping strings to arbitrary, possibly nested, strings,
numbers, arrays, objects and booleans. These are the variables that the associated
template should be rendered with.

**`partials`** is a JSON object mapping strings to strings. You can think of it as a
mock file system for testing `{% include %}` and `{% render %}`.

**`error`** is a boolean indicating if the test case should raise/throw and
exception/error.

**`strict`** is a boolean indicating if the test should be rendered in "strict mode",
if the target environment has a strict mode.

## More Information

`golden_liquid.json` and `golden_liquid.yaml` are currently generated from [these files](https://github.com/jg-rp/liquid/tree/main/liquid/golden) in the [Python Liquid repository](https://github.com/jg-rp/liquid). The plan is to move these source files (or some equivalent files) to this repository, so we might add test cases for behavior that Python Liquid chooses not to implement.

In the mean time, be sure to keep an eye on Python Liquid's [known issues page](https://jg-rp.github.io/liquid/known_issues) and [issue tracker](https://github.com/jg-rp/liquid/issues).

## Results Summary

This table summarizes the results of running version 0.14.0 of this test suit against the five Liquid engines with runners included in this repository.

| Engine                                                | Version | Passed | Failed |
| ----------------------------------------------------- | ------- | ------ | ------ |
| [Ruby Liquid](https://github.com/Shopify/liquid)      | 5.4.0   | 804    | 0      |
| [LiquidJS](https://github.com/harttle/liquidjs)\*\*   | 10.4.0  | 565    | 239    |
| [liquidpy](https://github.com/pwwang/liquidpy)        | 0.7.6   | 380    | 424    |
| [LiquidScript](https://github.com/jg-rp/liquidscript) | 1.6.0   | 800    | 4      |
| [Python Liquid](https://github.com/jg-rp/liquid)      | 1.8.0   | 804    | 0      |

\*\* It's worth noting that many, but not all, of the failed test cases for LiquidJS are due to the way it handles excess and/or unexpected filter arguments.
