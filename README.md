# Fuzzy Search

> [!NOTE]
> This is a fork of [lithammer/fuzzysearch][5] package with the addution of go 1.18 generics in all `Find` and `RankFind` functions.

Inspired by [bevacqua/fuzzysearch][1], a fuzzy matching library written in
JavaScript. But contains some extras like ranking using [Levenshtein
distance][2] and finding matches in a list of words.

Fuzzy searching allows for flexibly matching a string with partial input,
useful for filtering data very quickly based on lightweight user input.

The current implementation uses the algorithm suggested by Mr. Aleph, a russian
compiler engineer working at V8.

## Install

```
go get go.gopad.dev/fuzzysearch
```

## Usage

```go
package main

import "go.gopad.dev/fuzzysearch/fuzzy"

type item struct {
    Name  string
    Amount int
}

func (i item) FilterValue() string {
    return i.Name
}

func main() {
	fuzzy.Match("twl", "cartwheel")  // true
	fuzzy.Match("cart", "cartwheel") // true
	fuzzy.Match("cw", "cartwheel")   // true
	fuzzy.Match("ee", "cartwheel")   // true
	fuzzy.Match("art", "cartwheel")  // true
	fuzzy.Match("eeel", "cartwheel") // false
	fuzzy.Match("dog", "cartwheel")  // false
	fuzzy.Match("kitten", "sitting") // false

	fuzzy.RankMatch("kitten", "sitting") // -1
	fuzzy.RankMatch("cart", "cartwheel") // 5

	words := []item{
        {
            Name:  "cartwheel",
            Amount: 1,
        },
        {
            Name:  "foobar",
            Amount: 2,
        },
		{
            Name:  "wheel",
            Amount: 0,
        },
		{
            Name:  "baz",
            Amount: 10,
        },
    }
	fuzzy.Find("whl", words) // [cartwheel wheel]

	fuzzy.RankFind("whl", words) // [{whl cartwheel 6 0} {whl wheel 2 2}]

	// Unicode normalized matching.
	fuzzy.MatchNormalized("cartwheel", "cartwhéél") // true

	// Case insensitive matching.
	fuzzy.MatchFold("ArTeeL", "cartwheel") // true
}

```

You can sort the result of a `fuzzy.RankFind()` call using the [`sort`][3]
package in the standard library:

```go
matches := fuzzy.RankFind("whl", words) // [{whl cartwheel 6 0} {whl wheel 2 2}]
sort.Sort(matches) // [{whl wheel 2 2} {whl cartwheel 6 0}]
```

See the [`fuzzy`][4] package documentation for more examples.

## License

MIT

[1]: https://github.com/bevacqua/fuzzysearch
[2]: http://en.wikipedia.org/wiki/Levenshtein_distance
[3]: https://pkg.go.dev/slices#Sort
[4]: https://pkg.go.dev/github.com/gopad-dev/fuzzysearch/fuzzy
[5]: https://github.com/lithammer/fuzzysearch