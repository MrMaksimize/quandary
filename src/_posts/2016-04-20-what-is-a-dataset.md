---
layout: post
title: What Is A Dataset? A Delicious Explanation.
image: http://mrm-screen.s3.amazonaws.com/tetris-food.png
image_caption: image cc andromache on flickr.
author:
  name: Maksim Pecherskiy
  twitter: mrmaksimize
---

If you work with data, or interact with people who do, you have inevitably heard the word "dataset".  It's one of those jargon-y words that gets thrown around, and people are somehow supposed to know what it means. It's like when you go to the doctor, and he tells you that you will need "labrum repair", and you feel stupid asking what a labrum is.  

[Wikipedia's Definition of a Dataset](https://en.wikipedia.org/wiki/Data_set):
> Most commonly a data set corresponds to the contents of a single
> database table, or a single statistical data matrix, where every column
> of the table represents a particular variable, and each row corresponds
> to a given member of the data set in question.

And [data.gov's definiton](http://www.data.gov/glossary) is:
> A dataset is an organized collection of data. The most basic
> representation of a dataset is data elements presented in tabular form.
> Each column represents a particular variable. Each row corresponds to a
> given value of that column’s variable. A dataset may also present
> information in a variety of non-tabular formats, such as an extended mark
> -up language (XML) file, a geospatial data file, or an image file.


Nothing is incorrect about these definitions, but they tend to miscommunicate and under-explain what a dataset is. This causes a lot of confusion and frustration in the government open data community (and probably other communities as well). People end up thinking that there are datasets laying around on a shelf somewhere -- that it's a finite, well-defined thing that a city employee can just grab and publish.  Sometimes, it's true.  However, more often than not, a dataset is more like this:

![Cookies](http://take.ms/AZ3WU)

There are two properties that every dataset has that are critical to how useful it is -- **format** and **structure**.  

## Format
Format is fairly straightforward.  In order for a dataset to be used by as many people as possible, it has to be open and machine readable. This is just a primer - I'll go into more detail in a different post. 

### Open
Open formats are those that can be read by a simple text editor.  For example, CSV files - a common format for tabular data can be opened in Notepad.  They will be hard to read by a human, but it'll work. They can also be opened in Excel and plenty of languages have CSV parsers.  

However, the sister of the CSV file - the Excel file can **only** be opened in Excel.  That means that if I want to look at your file, I have to give Microsoft some serious $$ for Microsoft Office.   

### Machine Readable
The other aspect of an open dataset is that it's machine readable.  This ties in with "open" - since many more languages and libraries can read formats not copyrighted by a vendor.  But this also means structured in a predictable manner.  For example, a PDF file is not machine readable because it's digital paper - and breaking down the structure of a PDF is really hard for a program that can only deal with structured data - the format is closed and copyrighted, and also extremely opaque.

## Structure
Structure is a lot more difficult to get right than format. For the sake of this post (and to keep you awake), we'll talk only about tabular data (data that lives in rows and columns).  There are other types of data though, such as GIS (mapping) data, XML / JSON (nested data), and several others.  

Let's step back and take a look at a database (or the dough blob).   We'll keep NoSQL databases out of this for simplicity and only focus on the old-school relational databases.  What does a database look like?

Contrary to what Hollywood would like us to think, this is not a database:

<img src="https://c2.staticflickr.com/6/5142/5577388841_97e1280796_b.jpg" width="100%"/ alt="Not A Database">.  

A relational database isn't really that exciting - it's just a collection of tables (sorry, I tried, but relating this to cookies is hard):

![A Database](http://take.ms/MkaCS)

These tables contain a variety of columns. Invariably, one or more of these columns is going to have a set of values that relates to a set of values in another table.  That's what makes a database relational and also what gives the technology so much power and flexibility.  It's like dough - you can shape it, mold it, cut it and change its shape as much as you want to fit your cookie idea.  

<img alt="Dough is Flexible - You can make cookies or cupcakes" src="http://take.ms/9eaVo" width="100%" />

Let's take a look a more concrete example:  

Let's say you have a company that sells widgets.  You keep track of your customers, and you keep track of the widgets you sell.  Let's imagine a very simple database with just three tables:

![Simple Three Table Database](http://take.ms/y0Nyv)

There is one table to keep information on your customers, another one to keep track of the widgets you have, and a table that keeps track of the orders - where the customers bought the widgets. For the sake of simplicity, there is only one customer per order and one widget per order.  

Let's look at some example data in this type of structure and examine some points below: 

![Sample Widget Data](http://take.ms/OdQsB)

* We can see that each table has an ID column. This is called a **primary key** and is used for the database to differentiate each record in that specific table.  A lot of times it corresponds to a row number.
* In the orders table, we can see that each order has its own ID but also references the ID of the widget sold and the customer to whom it was sold.  

So with the following example, what are some datasets we can generate?  There are many options:

* We can get 3 datasets already just by using the tables themselves. So, there will be a customers dataset, a widgets dataset, and an orders dataset. However, the orders dataset will be pretty useless, since it references IDs only relevant in the context of the other two tables.  
* We can get a list of only those customers who have purchased a widget.  
* We can get a list of customers who purchased a widget and live in Chicago.  
* We can get a list of all the customers regardless of whether they have purchased a widget.  
* We can get a list of customers, the widget they each purchased, the model of the widget, and how much the widget costs.  
* We can get a list of widgets and which customers purchased them.  
* Or a list of widgets and in which city they were purchased.

We get these datasets by using SQL, a database query language, and what you see above is not an exhaustive list: there are many other alternatives.  In addition, due to data schema changes, process changes and technology changes, data after a certain date may not be valid or may not mean the same thing.  For example, up until our widget company changed their process in 2011, the "city" column in the customer table indicated the city in which the customer was born, not where he or she lived.  

This complexity is present in our simple fake database of just three tables, but modern relational databases have upwards of 100, often thousands of tables. There are also plenty of caveats in how they're named, how relations are structured, and what type of queries make sense. Plus, writing the queries is no easy task. 

![So Many Choices](http://take.ms/XDzwb) 

## Some Clarification
I want to take a moment to clarify here - I'm not saying that every dataset is hard to create.  Sometimes it's as easy as grabbing an Excel file from the desktop.  But even then, it's still necessary to make sure that there are no sensitive data in there, such as someone's phone number.  

I also haven't touched on things like aggregation and making sure that we generate data that fits the [Tidy Data Spec](http://vita.had.co.nz/papers/tidy-data.pdf) so that it's prime for analysis.

How do ***you*** define a dataset?  

I hope I was able to show you that it's not all cut and dry.  

The next time you go and ask someone for a dataset, bring them a box of cookies just in case. Fulfilling your request may involve a lot of hard work. 








