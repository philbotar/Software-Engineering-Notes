# Matching in Rust

Matching is like a super powered switch statement, or very clean if-else chain.

You give it a value, and it checks against a series of patterns until it finds one that fits.

```
let my_number = 3;

match my_number {
    1 => println!("It was one!"),
    2 => println!("It was two!"),
    3 => println!("It was three!"),
    _ => println!("It was something else."),
}
```

## Whats the Catch with Match?

The catch with Match is that it must be exhaustive. You are either putting in each possible combination, or you have a catch-all denoted as "_".


## Whats the best use-case for Match?

For Enums match is the best. It allows you to match the value and perform logic dependent on the value. Its also the safest way to do it and allows for exhaustive coding when required without missing a beat.

```
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

let light = TrafficLight::Green;

match light {
    TrafficLight::Red => println!("STOP!"),
    TrafficLight::Yellow => println!("Slow down!"),
    TrafficLight::Green => println!("GO!"),
}

```
