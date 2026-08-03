<h1 align="center">sparkline.mq</h1>

A pure Unicode sparkline renderer implemented as an [mq](https://github.com/harehare/mq) module.
Converts a numeric array into a compact `▁▂▃▄▅▆▇█` string that can be embedded directly in
Markdown output — handy for things like "commits per day over the last week" in a
report or README, without pulling in a charting library.

## Features

- Min-max normalization across the whole input array, mapped onto 8 tick levels
  (`▁▂▃▄▅▆▇█`)
- Handles negative numbers (normalization is shift-invariant)
- Well-defined edge cases: empty array, single value, and all-equal values
- Pure text output — no rendering engine, so it drops straight into Markdown,
  terminal output, or anywhere else a string fits

## Installation

Copy `sparkline.mq` to your mq module directory, or place it anywhere and reference it with `-L`.

```sh
cp sparkline.mq ~/.local/mq/config/
```

### HTTP Import (no local installation needed)

If `mq` was built with the `http-import` feature, you can import directly from GitHub without any local setup:

```sh
mq -I raw 'import "github.com/harehare/sparkline.mq" | sparkline::sparkline' data.json
```

Pin to a specific release with `@vX.Y.Z`:

```sh
mq -I raw 'import "github.com/harehare/sparkline.mq@v1.0.0" | sparkline::sparkline' data.json
```

## Usage

```sh
mq -L /path/to/modules -I raw \
  'import "sparkline" | sparkline::sparkline' data.json
```

If you copied it to the mq built-in module directory:

```sh
mq -I raw 'import "sparkline" | sparkline::sparkline' data.json
```

## API

### `sparkline(numbers)`

Converts an array of numbers into a Unicode sparkline string, one tick character
(`▁▂▃▄▅▆▇█`) per element.

| Input type | Output |
|---|---|
| Array of numbers | Sparkline string, same length as the input array |
| `[]` | `""` |

Values are min-max normalized across the whole array, so only the relative shape of
the series is preserved, not its absolute magnitude.

Edge cases:

- **Empty array** — returns `""`.
- **Single value, or all values equal** — every tick is the middle character (`▅`),
  since there is no range to normalize against.
- **Negative numbers** — normalized the same as positive numbers.

Raises an error if any element is not a number.

## Example

```sh
mq -I raw 'import "sparkline" | [3, 5, 2, 8, 12, 4, 6] | sparkline::sparkline' <<< ""
# => "▂▃▁▅█▂▄"
```

Embedding a sparkline directly in a Markdown report:

```sh
mq -I raw 'import "sparkline" as s
[3, 5, 2, 8, 12, 4, 6] | "Commits this week: " + s::sparkline()' <<< ""
# => "Commits this week: ▂▃▁▅█▂▄"
```

## Compatibility

Requires [mq](https://github.com/harehare/mq) v0.6 or later.

## License

MIT
