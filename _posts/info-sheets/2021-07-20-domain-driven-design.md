---
layout: post
title: "Domain-Driven Design"
author: alfred
categories: [info sheet, design, ddd]
featured: false
hidden: false
---
Domain-Driven Design is an approach to software development that centers the development on programming a domain model that has a rich understanding of the processes and rules of a domain. The name comes from a book by Eric Evans that describes the approach through a catalog of patterns.

At the heart of this approach was the idea that to develop software for a complex domain, we need to build **Ubiquitous Language** that embeds domain terminology into the software systems that we build. DDD stresses developing domain models in software, and evolving them during the life of the software product.

The book introduced the notion of classifying objects into **Entities**, **Value Objects**, and **Service Objects** and identifying the concept of **Aggregates**. A particularly important part of DDD is the notion of Strategic Design - how to organize large domains into a network of **Bounded Contexts**.

<!--more-->

### References

The book [Domain-Driven Design: Tackling Complexity in the Heart of Software](https://learning.oreilly.com/library/view/domain-driven-design-tackling/0321125215/) written by Eric Evans is the reference work on the theory of domain-driven design.

The book [Implementing Domain-Driven Design](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/) written by Vaughn Vernon is a more practical book with sample code illustrating how to implement a domain-driven design. It comes with two sample repositories:

* [Clone Java implementation](https://github.com/VaughnVernon/IDDD_Samples.git)
* [Clone partly .NET implementation](https://github.com/VaughnVernon/IDDD_Samples_NET)

The free ebook [Domain Driven Design Quickly](https://www.infoq.com/minibooks/domain-driven-design-quickly/) produced by [InfoQ.com](http://InfoQ.com)  is a short, quickly-readable summary and introduction to the fundamentals of DDD; it does not introduce any new concepts; it attempts to concisely summarize the essence of what DDD is.
