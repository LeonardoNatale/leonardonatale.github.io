---
title: "The Great Pizza Clean-Up"
description: "Showcasing Lea: our secret weapon in the battle against pizza pretenders. Lea helps us slice through the clutter, identifying and tossing out the most outrageous pizza claims."
date: 2023-12-02T09:03:20-08:00
---

## Motivation

In an age where culinary creativity often crosses into the realm of the absurd, the sanctity of pizza is under threat. Everywhere you look, there's a new, wild rendition being labeled as 'pizza'. Not every round dough with toppings earns the right to be called a pizza.

In 'The Great Pizza Clean-Up', we embark on a flavourful journey, armed with [Lea](https://github.com/carbonfact/lea) and a dash of Italian flair, to distinguish the authentic from the outrageous.

It's time to restore the honour of this beloved dish, one data point at a time. Join me in redefining what truly makes a pizza, a pizza.

## The data

In my quest to demonstrate the simplicity of [Lea](https://github.com/carbonfact/lea), I stumbled upon the perfect candidate: the [Pizza Place Sales dataset.](https://www.mavenanalytics.io/data-playground?search=pizza%20place%20sales) It consists of a year's worth of sales from a fictitious pizza place, including the date and time of each order and the pizzas served, with additional details on the type, size, quantity, price, and ingredients.

But here's the twist: amongst these pizze lie some so wildly unconventional that they just can't make the cut. So, as we crunch through this data pizza, we'll be plucking out these over-the-top pizze to keep our analysis authentic.

Armed with Lea and our discerning taste for true pizza, we'll tackle tantalising questions, all while sidestepping the pizza imposters:

1. How many customers do we have each day? Are there any peak hours?
2. How many pizzas are typically in an order? Do we have any bestsellers?
3. How much money did we make this year? Can we identify any seasonality in the sales?

Join me as we slice through the data, separating the pizza wheat from the chaff.

### Setting up

All the essential code for our analysis is available [here](https://github.com/LeonardoNatale/the-great-pizza-cleanup).

Our data journey begins with four key CSV files, which you'll find in the **`seeds/`** folder [here](https://github.com/LeonardoNatale/the-great-pizza-cleanup/tree/main/seeds). These files are the foundational ingredients of our pizza-themed exploration. Our goal is to integrate them into our DuckDB database under a **`raw`** schema, and [Lea](https://github.com/carbonfact/lea) is the tool that simplifies this process.

We organise our workspace by creating a **`views/`** folder, with a **`raw/`** subfolder within it, for managing our data transformations. Think of it as setting up the prep area for our data analysis. Here’s how we smoothly transfer our CSV files into the DuckDB database using pandas. It's important to note that the name of the dataframe will serve as the table name in DuckDB. Here's a quick glimpse of the process:

```python
import pathlib
import pandas as pd

here = pathlib.Path(__file__).parent
pizzas = pd.read_csv(here.parent.parent / "seeds" / "pizzas.csv")
```

Our project structure, ready for data processing, will resemble the following ⬇️

```
views
├── raw
    ├── order_details.py
    ├── orders.py
    ├── pizza_types.py
    └── pizzas.py
```
