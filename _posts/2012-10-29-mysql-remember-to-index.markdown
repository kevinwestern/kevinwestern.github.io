---
layout: post
title: "MySQL: Remember to Index"
date: 2012-10-29 21:59
comments: true
categories: mysql database performance computer-science
---

The only thing worse than discovering your application is crashing is having a customer tell you your application is crashing. Not too long ago I received panicked emails stating that, at some random intervals, our application was throwing 500s. Unfortunately I turned off push notifications (actually it has helped separate work/life - if it's an emergency you'll be getting a call) so I didn't receive these emails until the next morning when I sat down at my desk.

My co-worker had already diagnosed the problem by the time I had arrived. The recent changes we had pushed included a 1s ajax poll on a particular page which, in most polling cases, ended up hitting the database. Also, those changes were going live with a few thousand more users than the previous version. My co worker discovered that the database was the culprit, but why?

It turns out one of tables we were querying on didn't have an index set for a particular column. Coupled with the fact we added more users it was obvious why the database was choking: it had to perform a full table scan. The solution to the problem was easy, of course, we just needed to add the appropriate index.

## Performance before and after indexing

It has been a few years since I've directly interacted with MySQL so I thought it would be fun to run a couple benchmarks on a table with and without indexing. The first thing I did was setup a Rackspace cloud server with 8 GB of memory and 4 vCPUs. If you don't have a testing box, I'd highly recommend it. You get great computing power at your fingertips on demand. The best part is: it's cheap. You can turn your machine off when it's not being used so you're not charged for something you're not using. It took me a couple hours to setup MySQL and write the python (I chose python to familiarize myself with it) scripts for inserting data. Two hours only cost me $0.96.

I created the following test data:

* 5 million rows of randomly generated users with: id, firstname, lastname, email, year, month and date.
* 100k randomly generated products with: id, name, category, price and views.
* 1 million rows of purchases consisting of: productid and userid.

The first query was searching for a particular email address.


{% highlight mysql %}
mysql> select count(*) from Users where email='test@test.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (2.58 sec)
{% endhighlight %}

That's pretty damn slow. How can we improve this? Let's add the constraint that email addresses will be unique. Next, we can add a unique index to our email column. Depending on your database engine you could define an index type (BTree or Hash). I decided to use the InnoDB engine which only supports a BTree index.

{% highlight mysql %}
mysql> create unique index user_email_index on Users (email);
Query OK, 0 rows affected (27.57 sec)
Records: 0  Duplicates: 0  Warnings: 0
{% endhighlight %}
With the index added let's see how much of an improvement that made:

{% highlight mysql %}
mysql> select count(*) from Users where email='test@test.com';
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)
{% endhighlight %}

What a huge improvement! The query was so much faster it couldn't be measured in seconds, perhaps if we had measured in milliseconds we would have a non zero response time.

### What changed?

The query without the index had to perform a full table sequential scan, meaning it had to look at all 5 million rows. Think of it as an array with 5 million elements we have to traverse. Adding the index transformed this array into a [self balancing tree](http://en.wikipedia.org/wiki/Self-balancing_binary_search_tree), more specifically, a [btree](http://en.wikipedia.org/wiki/B-tree). This means searching only takes O(log n) steps, or at most 23 steps (2^22 = 4.2 million, 2^23 = 8.3 million). This is a huge improvement over our previous 5 million steps!

### Another slow query

Let's suppose I want to find all products bought by users who were born in 1994 without using an index.

{% highlight mysql %}
mysql> select count(*) from Users u join Purchases p on u.Id = p.Id join Products pr on p.ProductId = pr.Id where u.Year = 1994;
+----------+
| count(*) |
+----------+
|    15378 |
+----------+
1 row in set (3.04 sec)
{% endhighlight %}

3.04 seconds - unacceptably slow.

{% highlight mysql %}
mysql> create index users_year_index on Users (Year);
Query OK, 0 rows affected (22.42 sec)
Records: 0  Duplicates: 0  Warnings: 0


mysql> select count(*) from Users u join Purchases p on u.Id = p.Id join Products pr on p.ProductId = pr.Id where u.Year = 1994;
+----------+
| count(*) |
+----------+
|    15378 |
+----------+
1 row in set (0.70 sec)
{% endhighlight %}

A 434% improvement! I'm sure this query could be improved even more, but I had to head out after running this last query, so this is where I stopped.

## Conclusion
When you'll be frequently querying on a particular field in your database remember to add an index for that column to increase performance and keep your customers happy!
