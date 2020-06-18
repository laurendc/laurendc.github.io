---
layout: post
title: "#100DaysofCode - Day2: Variables & Formatting"
---

Today's Progress: Finished variables and formatting, which dug into the [fmt](https://golang.org/pkg/fmt/) package methods.

Thoughts: Debating skipping some parts. It could also mean that I've underestimated how quickly I'd be able to jump back into this. Probably not a bad thing. It is mostly boring but I'm a big believer in trying not to skip ahead with foundational building block items. 

However, it was also nice to dig through [verbs](https://golang.org/pkg/fmt/#hdr-Printing).

Link to work: I'll add this fun snippet.

{% raw %}
```go
package main

import "fmt"

func main() {
  floatExample := 1.75

  fmt.Printf("Working with a %T", floatExample) 
  
  fmt.Println("\n***") 
  
  yearsOfExp, reqYearsExp := 3, 15

  fmt.Printf("I have %d years of Go experience and this job is asking for %d years", yearsOfExp, reqYearsExp) 
  
  fmt.Println("\n***") 
  
  stockPrice := 3.50

  fmt.Printf("Each share of Gopher feed is %.2f!", stockPrice)
}
```
{% endraw %}