---
title: "The Great Pizza Clean-Up"
description: "Showcasing Lea: our secret weapon in the battle against pizza pretenders. Lea helps us slice through the clutter, identifying and tossing out the most outrageous pizza claims."
date: 2023-12-02T09:03:20-08:00
---

## Motivation

In an age where culinary creativity often crosses into the realm of the absurd, the sanctity of pizza is under threat. Everywhere you look, there's a new, wild rendition being labeled as “pizza”. Not every round dough with toppings earns the right to be called a pizza.

In “The Great Pizza Clean-Up”, we embark on a flavourful journey. Armed with [Lea](https://github.com/carbonfact/lea), a minimalistic alternative to DBT developed by [Max Halford](https://maxhalford.github.io/), and a dash of Italian flair, we set out to distinguish the authentic from the outrageous.

It's time to restore the honour of this beloved dish, one data point at a time.

## The data

In my quest to showcase [Lea](https://github.com/carbonfact/lea)’s simplicity, I stumbled upon the perfect candidate: the [Pizza Place Sales dataset.](https://www.mavenanalytics.io/data-playground?search=pizza%20place%20sales) It consists of a year's worth of sales from a fictitious pizza place, including the date and time of each order and the pizzas served, with additional details on the type, size, quantity, price, and ingredients.

But here's the twist: amongst these pizze lie some so wildly unconventional that they just can't make the cut.

With [Lea](https://github.com/carbonfact/lea)'s capabilities and a little help from the pizza police 🚨, we're set to explore some intriguing aspects of our data, while carefully navigating around the pizza pretenders:

1. How many customers do we have each day? Are there any peak hours?
2. How many pizzas are typically in an order? Do we have any bestsellers?
3. How much money did we make this year? Can we identify any seasonality in the sales?

Join me as we slice through the data 🍕

## Setting up

All the essential code for our analysis is available [on GitHub](https://github.com/LeonardoNatale/the-great-pizza-cleanup).

Our data journey begins with four key CSV files, which you'll find in the **`seeds/`** folder [here](https://github.com/LeonardoNatale/the-great-pizza-cleanup/tree/main/seeds). These files are the foundational ingredients of our pizza-themed exploration. Our goal is to integrate them into our DuckDB database under a **`raw`** schema, and [Lea](https://github.com/carbonfact/lea) is the tool that simplifies this process.

We organise our workspace by creating a **`views/`** folder, with a **`raw/`** subfolder within it, for managing our data transformations. Think of it as setting up the prep area for our data analysis. Here’s how we smoothly transfer our CSV files into DuckDB using pandas. It's important to note that the name of the dataframe will serve as the table name in DuckDB. Here's a quick glimpse of the process:

```python
import pathlib
import pandas as pd

here = pathlib.Path(__file__).parent
pizzas = pd.read_csv(here.parent.parent / "seeds" / "pizzas.csv")
```

Our project structure, ready for data processing, will resemble the following ⬇️

```
views
└── raw
    ├── order_details.py
    ├── orders.py
    ├── pizza_types.py
    └── pizzas.py
```

## Preparing the data

With our raw data now successfully loaded, it's time to roll up our sleeves and delve deeper. A closer inspection of the **`pizzas`** and **`pizza_types`** tables reveals an opportunity for merging to streamline our analysis.

Take, for example, the data for “Barbecue Chicken Pizza” (a choice that might raise a few Italian eyebrows 🤨) in the **`pizzas`** table:

| pizza_id  | pizza_type_id | size | price |
| --------- | ------------- | ---- | ----- |
| bbq_ckn_s | bbq_ckn       | S    | 12.75 |
| bbq_ckn_m | bbq_ckn       | M    | 16.75 |
| bbq_ckn_l | bbq_ckn       | L    | 20.75 |

and from the `**pizza_types**` table:

| pizza_type_id | name                       | category | ingredients                                                                         |
| ------------- | -------------------------- | -------- | ----------------------------------------------------------------------------------- |
| bbq_ckn       | The Barbecue Chicken Pizza | Chicken  | Barbecued Chicken, Red Peppers, Green Peppers, Tomatoes, Red Onions, Barbecue Sauce |

A more intuitive format would merge these details, like so:

| pizza_type_id | name                       | category | size | price |
| ------------- | -------------------------- | -------- | ---- | ----- |
| bbq_ckn       | The Barbecue Chicken Pizza | Chicken  | S    | 12.75 |
| bbq_ckn       | The Barbecue Chicken Pizza | Chicken  | M    | 16.75 |
| bbq_ckn       | The Barbecue Chicken Pizza | Chicken  | L    | 20.75 |

And we'll relocate the ingredients into a separate table:

| pizza_type_id | ingredient_name   |
| ------------- | ----------------- |
| bbq_ckn       | Barbecued Chicken |
| bbq_ckn       | Red Peppers       |
| bbq_ckn       | Green Peppers     |
| bbq_ckn       | Tomatoes          |
| bbq_ckn       | Red Onions        |
| bbq_ckn       | Barbecue Sauce    |

Since our **`raw`** data is already nestled in our database, transforming it becomes a breeze with [Lea](https://github.com/carbonfact/lea). We can use a **`SELECT`** query for this transformation and place the resulting data in the **`staging/`** folder.

For example, our **`pizzas.sql`** file in the **`staging`** folder would look something like this:

```
SELECT
    pizza_type_id,
    name,
    category,
    size,
    price
FROM
    raw.pizzas
    JOIN raw.pizza_types USING (pizza_type_id)
```

We apply similar transformations to the **`orders`** and **`order_details`** data. For a detailed look at these queries, check out the [provided code](https://github.com/LeonardoNatale/pizza-engineering/blob/main/views/staging/orders.sql#L14).

After these steps, our **`views/`** folder is now neatly organised as follows ⬇️:

```
views
├── raw
│   ├── order_details.py
│   ├── orders.py
│   ├── pizza_types.py
│   └── pizzas.py
└── staging
    ├── orders.sql
    ├── pizza_ingredients.sql
    └── pizzas.sql
```

With our data neatly prepped and staged, we're all set for the deep-dive analysis.

## Pizza Police

🚨🚨🚨 Alert! The pizza police have entered the chat! 🚨🚨🚨

<div align="center" >
<figure style="max-width: 300px;">
    <img src="./pizza_police.png" style="box-shadow: none;">
</figure>
</div>

With the help of our newly crafted ingredients table, our vigilant pizza police have spotted some truly outrageous pizza claims. Alfredo sauce with chicken, chipotle sauce, pineapple, and even thai sweet chilli sauce on a pizza? This culinary chaos is simply unacceptable!

Thankfully, the pizza police, along with [Lea](https://github.com/carbonfact/lea), are here to enforce a standard of authenticity. It's time to establish some order in this pizza pandemonium. We're setting up a **`pizza_police/`** folder within our **`views/`** directory to sort things out. And let's get it straight once and for all: it's “pizze,” not “pizzas”!

As it turns out, maintaining pizza integrity is just a matter of a few well-placed **`SELECT`** queries on our staged data.

For example, here's how we ensure only the most authentic pizze make it through our filter:

```sql
SELECT
    *
FROM
    staging.pizzas sp
WHERE
    NOT EXISTS (
        SELECT
            1
        FROM
            staging.pizza_ingredients pi
        WHERE
            pi.pizza_type_id = sp.pizza_type_id
            AND pi.ingredient_name IN (
                'Alfredo Sauce',
                'Barbecued Chicken',
                'Chicken',
                'Chipotle Sauce',
                'Pineapple',
                'Thai Sweet Chilli Sauce'
            )
    );
```

We're applying a similar level of scrutiny to our **`orders`** as well. You can view this query [here](https://github.com/LeonardoNatale/pizza-engineering/blob/main/views/pizza_police/orders.sql#L16).

Now, our **`views/`** folder looks clean:

```
views
├── pizza_police
│   ├── orders.sql
│   └── pizze.sql
├── raw
│   ├── order_details.py
│   ├── orders.py
│   ├── pizza_types.py
│   └── pizzas.py
└── staging
    ├── orders.sql
    ├── pizza_ingredients.sql
    └── pizzas.sql
```

With the pizza police on our side, we're ready to answer our initial questions. With the assurance that only the most authentic pizze are included in our analysis.

## Analytics

It's time to crunch some numbers and turn our data into meaningful insights, all while respecting traditions.

### Crafting KPIs

First, we create an **`analytics/`** schema. Within this, we consolidate our primary queries into a **`kpis.sql`** file:

1. The daily customer count.
2. The average number of pizzas per order.
3. The total sales for the year.

Curious about the queries? Find them detailed [here](https://github.com/LeonardoNatale/pizza-engineering/blob/main/views/analytics/kpis.sql#L8). Below are the results:

| metric                  | value       |
| ----------------------- | ----------- |
| average_pizze_per_order | 1.9         |
| avg_orders_per_day      | 49.9        |
| total_sales             | 1,722,907 $ |
| total_orders            | 18,214      |

### Unraveling Seasonality in Sales

Our next step is to detect any seasonality in sales. For this, we craft a **`sales_by_month.sql`** query to break down sales data by month. Here's the query structure:

```sql
SELECT
    monthname(datetime) AS MONTH,
    SUM(quantity * price) AS total_sales
FROM
    pizza_police.orders
    JOIN pizza_police.pizze USING (pizza_type_id)
GROUP BY
    MONTH
ORDER BY
    total_sales DESC;
```

### Diving Deeper

We're not stopping there! We also have queries ready for identifying peak hours (**`peak_hours.sql`**) and our top 10 best-selling pizzas (**`top10_bestseller.sql`**), all neatly organized under the **`analytics`** schema.

Now, our **`views/`** folder illustrates our comprehensive data journey:

```
views
├── analytics
│   ├── kpis.sql
│   ├── peak_hours.sql
│   ├── sales_by_month.sql
│   └── top10_bestseller.sql
├── pizza_police
│   ├── orders.sql
│   └── pizze.sql
├── raw
│   ├── order_details.py
│   ├── orders.py
│   ├── pizza_types.py
│   └── pizzas.py
└── staging
    ├── orders.sql
    ├── pizza_ingredients.sql
    └── pizzas.sql
```

And can be beautifully represented with a flowchart:

<div align="center" >
<figure style="max-width: 70%;">
    <img src="./mermaid-diagram.png" style="box-shadow: none;">
</figure>
</div>

## Wrapping It Up

The final step is to materialise our views with [Lea](https://github.com/carbonfact/lea). This is a straightforward process, detailed in the [readme](https://github.com/LeonardoNatale/pizza-engineering/blob/main/README.md#L19):

1. **`lea prepare`**
2. **`lea run`**
3. **`lea docs`**, if you want to generate some beautiful docs

With these commands, our schemas and tables are now fully operational in DuckDB, ready to deliver insightful analyses.

As we bring our exploration of pizza sales data to a close, the key to unlocking our insights has been [Lea](https://github.com/carbonfact/lea). This journey demonstrated that diving into data analysis doesn't require you to be a seasoned data engineer. Lea stands out as a testament to this, offering a user-friendly, minimalistic yet powerful tool for data processing and analysis.
