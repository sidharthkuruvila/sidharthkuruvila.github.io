---
layout: post
title: Human readable identifiers
date: 2018-02-09 15:49 +0530
---
This blog post came out of a conversation on how to design readable identifiers and what kinds of information can be put into them.

What is an identifier? Let’s start with a real world example in the form of a licence plate (eg. DL 11 CAA 1111). The text of a licence plate uniquely identifies every car in india. It tells you where the the car was registered and it might be possible to guess when the car was registered. When you order a book on flipkart you get an 18 digit order id to track that order on the flipkart website (eg. OD387592…). A github ticket has a numeric id that increments with every new ticket added to a project. The ticket also has a url that uniquely identifies the ticket resource.

Lets define some characteristics of a human readable identifier. It should be easy to share verbally when you call support or when telling a friend the id. It can contain useful information. Going back to the licence plate example, where the car was registered for example. It should be easy to remember all or part of the identifier.

Now that we have defined the characteristics let’s look at some features that can help us achieve them.

*Separators* can make the an identifier easier to read. For example credit card numbers are usually represented with between every four digits (eg. 4024 0071 6863 1462). You can read the number four digits at a time. Amazon’s order ids consist of three parts separated by hyphens, a numeric prefix, and two 7 digit numeric strings (eg. #171-6556543-4724335). We could take flipkart’s order id (eg. OD387592038728388000) and add some hyphens to it to form OD:387592-038728-388000. This is a lot easier to read.

*Prefixes* are another way of improving readability. Static prefixes (eg.OD…) make it possible to for you to tell the type of an identifier, OD for example might indicate that the identifier is an order. Prefixes can also contain useful information that you can use. A flight booking might contain the date on which the flight leaves which you can check on the way to the airport (eg. 17070134234). Or a film ticket id might be prefixed with a code for the theater the film is playing in  (eg. INRC…, Inox Rcity Mall). Numeric prefixes like amazon’s can be a bit harder to read but can still be useful, 171 for example appears to be used for orders from india and D01 is used for ebooks.

*Numeric identifiers* are generally easier to share compared to alphanumeric ones but take more space (eg. OD3875... ). We need to take into account that decimal numbers do not map well to binary numbers. Hexadecimal identifiers advantage of fitting into binary strings  but are rarely used except for more technical applications like git commits. Alphanumeric ids can be harder to read than numeric one even though they take a lot less space, we could write the flipkart id as OD:2y0dws-hczzc4. We should avoid mixing case for example a and A in the same identifier as that would be harder to read aloud.


*Context* can help shorten identifiers. For example if orders ids are unique for a particular user there will be fewer order ids and shorter id lengths will be possible. Github does this with tickets ticket ids are incrementing numeric values and a ticket id is unique for a particular project (eg. https://github.com/sidharthkuruvila/ocaml-ide/issues/7)

*Shorter identifiers* are easier to remember and share. UUIDs make for bad identifiers, to guarantee uniqueness using a randomly generated id takes a lot of space, which in turn means that there is a lot more to read. Incrementing ids (eg. ID001, ID002, … , ID010,... ) can take a lot less space than randomly generated ids, we could encrypt the ids using some form of format preserving encryption. Making the prefix part of the unique id, can reduce the size of the changing part of the identifier, for example a date prefix (eg. 17070134234). Care should be taken, since the advantage would be lost if most identifiers share the same prefix, for example if the city name is the prefix and most business is done in a single city.

Two more strategies can be considered if we are willing to give up on uniqueness as a constraint. A substring or a non-unique hash of a unique identifier can be used as the human readable version of the identifier.  The systems will have to be designed to handle the possibility of duplicate entities with the same shortened identifier. Some extra deduplication can be done using other available information, the user’s identity for example. Identifiers can be created with shorter lifespans to be returned back to a common pool of identifiers after some period of time.

Here are a few points you should keep in mind when designing an identifier

 * Use separators to improve readability
 * Choose a suitable prefix or prefix strategy
 * Prefer numbers for the generated part of the identifiers
 * Choose a suitable identifier generation strategy.
 * Try to keep your identifiers small, or create a separate human readable identifier.
