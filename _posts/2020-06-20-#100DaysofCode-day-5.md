---
layout: post
title: "#100DaysofCode - Day5: Wrapping up Functions, Addresses, Pointers"
---

Today's Progress: Finished plowing through basic functions, cranked through a practical exercise. Pointers.

Thoughts: Started slow & I was quite easily distracted. However, this ended by the time I got to the exercise. The exercise itself wasn't too challenging but it was far more interesting than anything else that's happened with the course so far. Then jumped into addresses and pointers which was a good review for me. With that, I completed the course, yay!

Link to work: Here's our interstellar exercise.

{% raw %}
```go
package main

import "fmt"

func fuelGauge(fuel int) {
  fmt.Println("You have",fuel,"left")
} 

func calculateFuel(planet string) int {
  var fuel int
  switch planet {
  case "Venus":
    fuel = 300000
  case "Mercury":
    fuel = 500000
  case "Mars":
    fuel = 700000
  default:
    fuel = 0
  }
  fmt.Println("Planet is",planet,"and fuel is",fuel)

  return fuel
}

func greetPlanet(planet string) {
  fmt.Println("Welcome to", planet)
}

func cantFly(){
  fmt.Println("We do not have the available fuel to fly there.")
}

func flyToPlanet(planet string, fuel int) int {
  var fuelRemaining, fuelCost int
  fuelRemaining = fuel
  
  fuelCost = calculateFuel(planet)

  if fuelRemaining == fuelCost {
    greetPlanet(planet)
    fuelRemaining = fuelRemaining - fuelCost
  } else if fuelCost > fuelRemaining {
    cantFly()
  }
  return fuelRemaining
}

func main() {
  var fuel int
  fuel = 1000000
  planetChoice := "Venus"

  fuel = flyToPlanet(planetChoice, fuel)

  fuelGauge(fuel)
}
```
{% endraw %}
