---
title: Leetcode - Interesting SQL Problems
author: Brady Lamson
date: '2024-05-27'
slug: leetcode-interesting-sql-problems
categories: []
tags: []
description: ~
image: ~
math: ~
license: ~
hidden: no
comments: yes
toc: yes
---

# Overview

As I've been studying more SQL lately I figured this would be a good excuse to chronicle some of the more interesting problems. May as well get something out of doing the work.

# Leetcode Database Problem 607: Sales Person

[Link to the problem here](https://leetcode.com/problems/sales-person/description/)

This is the question that prompted this blog post as it was pretty fun to wrestle with. 

This problem gives us the following db schema:

![](images/lc_db_607.png)

The example input is as such:

## Tables

### SalesPerson table:

| sales_id | name | salary | commission_rate | hire_date  |
|----------|------|--------|-----------------|------------|
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |

### Company table:

| com_id | name   | city     |
|--------|--------|----------|
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |

### Orders table:

| order_id | order_date | com_id | sales_id | amount |
|----------|------------|--------|----------|--------|
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |

## The Problem

We're tasked with finding the names of all the salespersons who did not have any orders related to the company with the name "RED". 

## The Solution

Seems simple enough. It's important to note that we have a LOT of information that won't be relevant here. A lot of this is just here to confuse you. The big thing with these database leetcode problems is honing in on the few columns we actually care about.

My goal here is to break problems this into two key steps.

1. Get a list of the `sales_id` values that have an order associated with the company "RED". 
2. Set up my main query to exclude any sales persons with a `sales_id` in that list.

What this means is that we have two queries here, an inner and an outer one. Fun! 

### Inner Query

Setting up this inner query involves joining the `Orders` and `Company` tables together. We're looking for any orders where the company id links to the company name being "RED". Since we're interested in getting additional information on the Orders table this will be a left join smushing the company table onto orders.

```
SELECT o.sales_id
FROM Orders o
LEFT JOIN Company c ON o.com_id = c.com_id
WHERE c.name LIKE '%red%'
```

This provides us a list of all the sales ids connected to the company Red! So now we can use this list in our outer query.

### Outer Query

This ones super simple. We just want the names of salespersons that are not in that list we just made. This is the easy part.

```
SELECT s.name
FROM SalesPerson s
WHERE s.sales_id NOT IN (
    # Creates list of sales_id associated with company "Red"
    SELECT o.sales_id
    FROM Orders o
    LEFT JOIN company c ON o.com_id = c.com_id
    WHERE c.name LIKE 'RED'
)
```

And that's it! Not too bad. I'm still trying to build up intuition for nested queries so I wanted to save this one. I've got a LOT of SQL leetcode to go so I'll be progressively adding to this post over time.