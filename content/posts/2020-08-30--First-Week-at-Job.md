---
title: First week at Job as a Software Engineer
date: "2020-08-30T23:46:37.121Z"
template: "post"
draft: false
slug: "first-week-at-job"
category: "Programming"
tags:
  - "Java"
  - "Java Spring"
  - "Programming"
  - "Coding"
  - "Job"
  - "Spring"
  - "Pagination"
description: "My first task was related to pagination of the data to be displayed on a listing page of a web app. The system has thousands of products, and currently we were displaying the item codes according to the number of product instead of actual number of item codes. In this post I will be going to talk about the concepts of Java Spring that I came around to complete this task.
"
socialImage: "/media/image-2.jpg"
---


# Before we Begin
I have been wanting to get started with writing blog. I find it fun to read others blogs. Reading experiences of other Devs is useful. So here's my first blog post. I wanted it to be something special, hence the title.
Right now I am not sure of how much and what should my blog contain, I will be just going with what I feel is ok to be expressed, with some experience and feedback I hope to improve.

# So...
My first task was related to pagination of the items to be displayed on a listing page of a web app. The system has thousands of products, and currently we were displaying the item codes according to the number of product instead of actual number of item codes. In this post I will be going to talk about the concepts of Java Spring that I came around to complete this task.

The code for pagination already existed for the products. And so I thought it is going to be an easy task, I just need to remove the pagination from product and use it at the item code level. But alas, I was so wrong because if I used pagination on item codes then I will get all the products first and then the pagination will be applied on the item. I tried other unsuccessful approaches, trying to find shortcuts to complete the work, but instead I was going full circles with these approaches. I started working on these hacks with full hope and enthusiasm, but when I started to implement I realised how wrong the idea and thus I was back at the begining, but now with another method that I know wont work.

So, I finally went on with the approach of writing a join query for product and item, as this will allow me to control the number of rows being displayed. Now with the backstory over, lets start with the concepts that I learned to solve my first task.

## JPARepository
Its a part of `spring-boot-starter-data-jpa`. From the spring site  
  
 *`Data` makes it easy to use data access technologies, relational and non-relational databases, map-reduce frameworks, and cloud-based data services*.  
   
 It allows us to define a repository interface for each entity class. JPARepository is same as CrudRepository with the difference that JPA provides functionality for pagination and sorting. You can find good resource to get started from [here](https://stackoverflow.com/questions/32679406/how-does-spring-data-jpa-work-internally#32679494) and [here](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-creation)

## Pagination
This not only allows us to divide the result into several pages but also to sort them. In my case I had to sort them by product. We can pass a pageable object to the JPARepository interface to use pagination.  

What I wanted to use was a native query along with pagination. Due to my lack of knowledge of spring framework I googled my problem with not-so-apt-keywords and so I got by this [stackoverflow question](https://stackoverflow.com/questions/45539358/selecting-from-multiple-tables-in-spring-jpa-with-pageable-and-sorting). Added to that, my bad habit of copy pasting the code to just make it work, I missed to see one small note that the author had writen:  
 "*Since this query does not contain grouping it's not necessary to use an additional `countQuery`*"

And so, not using `countQuery` caused an error something like `Unknown column 'productId' in order clause`. This error made no sense to me and I took a lot of time to debug it but I still didn't get why it was throwing this error even though the code is same as in the stackoverflow answer. But my mentor came to the rescue and suggested to use `offset` and `limit` in the query instead.  

Now that I know my mistake, the right stackoverflow question should be [Spring Data and Native Query with pagination](https://stackoverflow.com/questions/38349930/spring-data-and-native-query-with-pagination) (didnt try though) and, of course, this error that I faced was also experienced by [someone else](https://stackoverflow.com/questions/21549480/spring-data-fetch-join-with-paging-is-not-working) about 6 years ago. So, is it that a more experienced developer just knows the right keyword to get the right result?

## Projections
By default the Spring repository returns one or more instance of a single entity. What should we do when we require selected columns from multiple entities? Projections come into the scene now. I know the query that I want to write and I know how to convert it into pages, only thing left is to get the result. Projections can be called something similar to `View` in databases in the sense they both *can join and simplify multiple tables into a single virtual table* [[4]](#References). 
Spring Data allows different types of projections:  
- Interface Based: Similar to `Entity` class
    * Closed Projections: function name is same as in the column name, optimise query
    * Open Projections: compute new value using `@Value` annotation
- Class based: no proxy(write your getter and setter), no nested projections
- Dynamic projections: decide return type at the runtime  
Refer here for more details [[5] [6]](#References)

I found Closed Interface Projections appropriate for my task. They are easy to use and the code looks concise.  

With these and a few structural changes I was able to complete this task which looked huge before getting here. 

I recommend "Java Brains" Spring tutorial if you want to get started with Spring. I have provided all the links which I used to solve this task.

## References:
[1] https://spring.io/projects/spring-data  
[2] http://zetcode.com/springboot/jparepository/  
[3] https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters  
[4] https://en.wikipedia.org/wiki/View_(SQL)  
[5] https://www.baeldung.com/spring-data-jpa-projections  
[6] https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#projections  