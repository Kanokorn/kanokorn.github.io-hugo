---
title: "Test refactoring with Table-driven"
date: 2019-08-24T18:17:47+07:00
draft: false
summary: "I used table-driven to refacotor my code. The output is now easier to add more test cases without having to create a new test case. We only need to add input and output. Also easier to read."
---

I used table-driven tests to refactor my code. I found this technique in Andrew Gerrand slide while he was talking at [Testing Technique](https://talks.golang.org/2014/testing.slide#1) in Google I/O 2014. I want to share this technique because it makes my code look better.

For example, I want to truncate a string to 20 characters in Go, which have 3 cases as shown below:

- string length shorter than 20 characters (abcdefghi)
- string length equal 20 characters (abcdefghijklmnopqrst)
- string length longer than 20 characters  (abcdefghijklmnopqrstuvwxyz)

Here is my “Truncate” function:

```
func Truncate(str string, limit int) string {
     originalString := str
     rs := []rune(originalString)

     if len(rs) > limit {
           originalString = string(rs[0:limit])
     }

     return originalString
}
```

Below is my old test before refactoring:

```
func TestTruncate_LengthLessThan20(t *testing.T) {
        result := Truncate("abcdefghi")
        require.Equal(t, "abcdefghi", result)
}
func TestTruncate_LengthEqual20(t *testing.T) {
        result := Truncate("abcdefghijklmnopqrst")
        require.Equal(t, "abcdefghijklmnopqrst", result)
}
func TestTruncate_LengthMoreThan20(t *testing.T) {
        result := Truncate("abcdefghijklmnopqrstuvwxyz")
        require.Equal(t, "abcdefghijklmnopqrst", result)
}
```

We can refactor it by using go struct to create table-driven tests like this:

```
func TestTruncateString(t *testing.T) {
     var flagtests = []struct {
         input  string
         output string
     }{  // input                         output
         {  "abcdefghi",                  "abcdefghi"},
         {  "abcdefghijklmnopqrst",       "abcdefghijklmnopqrst"},
         {  "abcdefghijklmnopqrstuvwxyz", "abcdefghijklmnopqrst"},
     }
     for _, tt := range flagtests {
         require.Equal(t, tt.output, Truncate(tt.input, 20))
     }
}
```

See! Our test is now easier to add more test cases without having to create a new test case. We only need to add input and output. Also easier to read. You can play truncate feature [here](https://play.golang.org/p/Ld0ErosWhd2)

That’s all for today. I hope you like my post. I would be glad to hear your opinions about the content.
