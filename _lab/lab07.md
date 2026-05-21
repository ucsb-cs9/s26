---
layout: lab
num: lab07
ready: true
desc: "Apartment Listing Manager"
assigned: 2026-05-21 11:00:00.00-7
due: 2026-05-26 23:59:59.59-7
---

# Learning Goals

In this lab, we will utilize many concepts covered in the course so far including:

* Inheritance and Polymorphism
* Implementing and applying the Heap data structure as a priority queue
* Testing your functionality with pytest

**Note: It is important that you try and start this lab early so you can utilize our office hours to seek assistance / ask clarifying questions during the weekdays before the deadline if needed!**

# Introduction

The goal for this lab is to write a program that will manage apartment listings. Each listing will have a date indicating when the apartment will become available. Listings will be organized in a **MinHeap** priority queue, ensuring the apartment that becomes available the soonest is listed first.


In this lab, you will develop various Apartment classes (`Apartment`, `StudioApartment`, and `FamilyApartment` that utilize inheritance and polymorphism), a `ApartmentListing` class representing a collection of apartments available on a specific date, and a `ListingQueue` class that organizes these apartment listings in a **MinHeap** based on their availability dates. This structure ensures that the apartment that becomes available the soonest is processed first, optimizing the management of apartment rentals.

You will also write pytests in `testFile.py` illustrating your behavior works correctly. This lab writeup will provide some test cases for clarity, but the Gradescope autograder will run different tests shown here.

## Cybersecurity Context
The skills developed in this lab directly translate to real-world cybersecurity applications.
The MinHeap priority queue you're implementing mirrors how security incident response systems use [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) to prioritize and process threats based on severity and timestamp, ensuring critical vulnerabilities are addressed first.
For example, the [MITRE ATT&CK Framework](https://attack.mitre.org/) is the Industry-standard for categorizing cyber threats using object-oriented principles. 

Your implementation of _inheritance_ and _polymorphism_ will reflect how security frameworks categorize and respond to different types of threats through a unified interface.
Your implementation of _getters_ and _setters_ demonstrates core security concepts like data encapsulation and controlled access, which are vital for maintaining data integrity and preventing unauthorized modifications. Finally, the `pytest` testing methodology parallels security testing practices where systems are rigorously evaluated against normal operations, edge cases, and potential vulnerabilities, just like they do in the [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/).

These programming patterns and structures are foundational in designing secure systems that effectively monitor, prioritize, and respond to security events in real-world cybersecurity operations.


# Instructions

You will need to create six files:
* `Apartment.py` - Defines a Apartment class representing commonality for all Apartments. For simplicity, this class will assume all Apartments have a `price` and `view`.
* `StudioApartment.py` - Defines a child class of Apartment. This class should inherit all fields / methods from the Apartment class, but also introduces the concepts of amenities a customer can add (represented as a list of strings).
* `FamilyApartment.py` - Defines a child class of Apartment. This class should inherit all fields / methods from the Apartment class. Famiy apartments are defined by a layout attribute and all have a set price depending on the apartment view.
* `ApartmentListing.py` - Defines a class that is a collection of apartment objects the customer wants to view. The total price for the viewings can be derived from each individual apartment price. This class will also have an expected date of when the customer would like to view the apartments on the listing. More details on this later in the writeup.
* `ListingQueue.py` - Defines a MinHeap to organize and process Apartment Listings according to their expected date of showing. You can adapt the Heap implementation shown in the textbook supporting the specifications in this lab.
* `testFile.py` - This file will contain your pytest functions that tests the overall correctness of your class definitions.

There will be no starter code for this assignment, but rather class descriptions and required methods are defined in the specification below.

You should organize your lab work in its own directory. This way all files for a lab are located in a single folder. Also, this will be easy to import various files into your code using the `import / from` technique shown in lecture.

# Apartment.py

The `Apartment.py` file will contain the definition of a Apartment base class. We will define the Apartment attributes as follows:

* `price` - float that represents the price of an apartment. Since the price will be defined by what type of apartment is listed, we can simply create the price field and initialize it to 0.0.
* `view` - str that describes the type of view available from the apartment. For simplicity, we can have three valid apartment views and label these with `"C"` for city view, `"P"` for park view, and `"L"` for lake view.

You should write a constructor that allows the user to construct an Apartment object by passing in values for the view. Your constructor should also create a price attribute and set it to 0.0.

* `__init__(self, view)`

Your Apartment class definition should also support the "getter" and "setter" methods for the price and view. Since this will be a base class for other Apartment types, anything we write here can be inherited by its child classes.

* `get_price(self)`
* `set_price(self, price)`
* `get_view(self)`
* `set_view(self, view)`

# StudioApartment.py and FamilyApartment.py

`Apartment` objects can be two different types. Both of these types of apartments inherit from the Apartment class:
1. `StudioApartment` that allows the customers to add additional amenities of their choice
2. `FamilyApartment` that has already been pre-configured and has a fixed price based on its view

## StudioApartment.py
The `StudioApartment` class will contain a constructor that takes in the view of the Apartment, and should use this view to call our base class' constructor. In addition to the view, it will initialize an amenities list represented as a Python List.

The price of a StudioApartment is defined by two things:
1. the view of the apartment
2. the number of amenties the apartment will have (assuming no amenities, it is an unfurnished simple apartment). StudioApartment will have the following fixed prices based on its view:

* City (C): $1,200
* Park (P): $1,500
* Lake (L): $1,800

The view from the apartment also dictates the number of additional amenities will cost based on the following definition:

* City (C): + $50.75 per additional amenity
* Park (P): + $60.50 per additional amenity
* Lake (L): + $80.00 per additional amenity

Since we now know that the price of an apartment should be based on the view, the `StudioApartment` constructor should determine the base price of the apartment and set it appropriately (remember, no need to write it in this class if we already have the method in `Apartment.py`).

* `__init__(self, view)`

There are two more methods this class should support:

* `add_amenity(self, amenity)` - this method will add to the amenities list and update its price appropriately. The amenity parameter is represented as a `str` type.

* `get_apartment_details(self)` - this method will construct and return a string containing the details of the `StudioApartment` object including the view, amenties, and price of the `StudioApartment`. An example (with escape characters shown for formatting) is given below. When constructing your string, please follow the **EXACT** format since this is what Gradescope will expect.

`StudioApartment` without amenities example:

```python
sa1 = StudioApartment("C")

assert sa1.get_apartment_details() == \
"""\
STUDIO APARTMENT
View: C
Amenities:
Price: $1200.00
"""
```

`StudioApartment` with a list of amenities example (note that each amenity will be indented with a tab, followed by a `+` sign):

```python
sa1 = StudioApartment("L")
sa1.add_amenity("balcony/patio")
sa1.add_amenity("dishwasher")

assert sa1.get_apartment_details() == \
"""\
STUDIO APARTMENT
View: L
Amenities:
\t+ balcony/patio
\t+ dishwasher
Price: $1960.00
"""
```

Note that for the assert statements, we need a backslash at the end of `assert sa1.get_apartment_details() == \` to allow for multi-line asserts, and we use a backslash after the quotes (on the line after that) because every time we indent a new line in the multi-line string, it will inherently add a \n, but we DON'T want to start our string with \n. Ex:
```
"\nSTUDIO APARTMENT\n" == """
STUDIO APARTMENT
"""
```

## FamilyApartment.py

A `FamilyApartment` class definition will exist in `FamilyApartment.py`. Similar to a `FamilyApartment` object, the class constructor will take in the view as well as the layout type of the apartment.

* `__init__(self, view, layout)`

Also similar to the `StudioApartment` class, `FamilyApartment` will use the view to set its price appropriately. The price of a `FamilyApartment` is defined as follows:

* City (C): $2,500
* Park (P): $3,000
* Lake (L): $3,500

Unlike studio apartments, family apartments do not have a list of amenities associated with it, but do have a unique layout that will be displayed when getting details for this apartment. This class should also have its own `get_apartment_details` method described below:

* `get_apartment_details(self)` - this method will construct and return a string containing the details of the `FamilyApartment` object including the view and layout of the `FamilyApartment`. An example (with escape characters shown for formatting) is given below. When constructing your string, please follow the **EXACT** format since this is what Gradescope will expect.

A sample output test for `get_apartment_details()`:

```python
fa1 = FamilyApartment("C", "Modern")
assert fa1.get_apartment_details() == \
"""\
FAMILY APARTMENT
View: C
Layout: Modern
Price: $2500.00
"""
```

Note that for the assert statements, we need a backslash at the end of `assert sa1.get_apartment_details() == \` to allow for multi-line asserts, and we use a backslash after the quotes (on the line after that) because every time we indent a new line in the multi-line string, it will inherently add a \n, but we DON'T want to start our string with \n. Ex:
```
"\nSTUDIO APARTMENT\n" == """
STUDIO APARTMENT
"""
```

# ApartmentListing.py

The `ApartmentListing` class will be defined in `ApartmentListing.py`. This class will keep track of various apartments for a single listing. The `ApartmentListing` class will have the following attributes:

* `apartments` - a Python List containing all the apartments that a single listing contains. This can be initially set to an empty list.
* `date` -  an int representing the expected date when the listing is available for viewing. This field will be used in determining the *priority of listing viewings*. Thus, it's possible for earlier reservations to be deprioritized based on the listing's availability or scheduled viewings.

The constructor for an `ApartmentListing` will take in the expected date that listing should be ready:

* `__init__(self, date)`

The date format will be stored as an `int` in a `YYYYMMDD`. This format keeps the date attributes straightforward and allows for easy sorting and comparison of dates. Here's how you would represent a few dates in this format:

* January 1, 2024 --> 20240101
* May 15, 2019 --> 20190515
* October 31, 2022 --> 20221031

In addition to the constructor, you will need to implement getters for the date attribute, the ability to add Apartment objects to the listing, as well as a method to construct a string representing the listing details:

* `get_date(self)`
* `add_apartment(self, apartment)` - will add the apartment object to the end of the Python List
* `info(self)` - constructs and returns a string containing the date of the listng, all information for each apartment in the listing, as well as the total listing price.
*
* Since we're storing various Apartment objects in this class, we can utilize **polymorphism** and simply call the `get_apartment_details()` method on the Apartment objects when constructing the string for our entire listing, as well as `get_price()` to compute the `ApartmentListing` total price.

An example of the `info()` string format is given below:

```python
sa1 = StudioApartment("L")
sa1.add_amenity("balcony/patio")
sa1.add_amenity("dishwasher")
fa1 = FamilyApartment("C", "Modern")
listing = ApartmentListing(20250101) #January 1, 2025
listing.add_apartment(sa1)
listing.add_apartment(fa1)

assert listing.info() == \
"""\
***
Date of Viewing: 20250101
STUDIO APARTMENT
View: L
Amenities:
\t+ balcony/patio
\t+ dishwasher
Price: $1960.00

----
FAMILY APARTMENT
View: C
Layout: Modern
Price: $2500.00

----
TOTAL APARTMENT LISTING PRICE: $4460.00
***
"""
```

# ListingQueue.py

The `ListingQueue` class will be defined in `ListingQueue.py`. This priority queue is implemented as a MinHeap data structure. The `ListingQueue` will manage `ApartmentListing` objects based on their `date` attribute.

* `__init__(self)` - the constructor for the `ListingQueue` will simply initialize the Python List representing the MinHeap.

In addition to the construction of the MinHeap in this class, two methods are required to be implemented:

* `add_listing(self, apartment_listing)` - an `apartment_listing` object will be stored in the MinHeap *prioritized by its date attribute* (lower value means **higher** priority)
* `process_next_listing(self)` - this method removes the root node from the MinHeap (and restructures the MinHeap), and returns a string containing the root value's apartment listing description. If the `ListingQueue` is empty, then it should return an empty string.

The automated tests will create various apartment listings with different date attributes. It will then call `process_next_listing()` one at a time and check the removed ApartmentListing is in the right priority by checking their expected `info()` string. You should write similar tests to confirm the MinHeap state is in the correct order.

If you need additional clarification on how to implement these methods and how to structure the ListingQueue, look [here in the textbook](https://runestone.academy/ns/books/published/pythonds/Trees/BinaryHeapImplementation.html).

For example:
```python
listing1 = ApartmentListing(20240417)   #April 17,2024
sa1 = StudioApartment("P")
listing1.add_apartment(sa1)
listing2 = ApartmentListing(20240115)   #January 15,2024
sa2 = StudioApartment("L")
sa2.add_amenity("balcony/patio")
sa2.add_amenity("dishwasher")
listing2.add_apartment(sa2)
listing3 = ApartmentListing(20240415)    #April 15,2024
fa1 = FamilyApartment("L", "Modern")
listing3.add_apartment(fa1)
listing4 = ApartmentListing(20240215)    #February 15,2024
fa2 = FamilyApartment("C", "Open")
listing4.add_apartment(fa2)

listingQueue = ListingQueue()
listingQueue.add_listing(listing1)
listingQueue.add_listing(listing2)
listingQueue.add_listing(listing3)
listingQueue.add_listing(listing4)

print(listingQueue.process_next_listing())
```

Output:
```python
***
Date of Viewing: 20240115
STUDIO APARTMENT
View: L
Amenities:
        + balcony/patio
        + dishwasher
Price: $1960.00

----
TOTAL APARTMENT LISTING PRICE: $1960.00
***
```

# testFile.py


This file should test all of your classes using pytest. Think of various scenarios and edge cases when testing your code according to the given descriptions. You should test every class' method functionality. Even though Gradescope will not use this file when running automated tests (there are separate tests defined for this), it is important to provide this file with various test cases (testing is important!!).

Of course, feel free to reach out and/or post questions on the forum as they come up!

# Submission

Once you're done with writing your class definitions and tests, submit the following files to Gradescope's Lab07 assignment:

* `Apartment.py`
* `StudioApartment.py`
* `FamilyApartment.py`
* `ApartmentListing.py`
* `ListingQueue.py`
* `testFile.py`

There will be various unit tests Gradescope will run to ensure your code is working correctly based on the specifications given in this lab.

If the tests don't pass, you may get some error message that may or may not be obvious at this point. Don't worry - if the tests didn't pass, take a minute to think about what may have caused the error. If your tests didn't pass and you're still not sure why you're getting the error, feel free to ask your TAs or Learning Assistants.

# Cross-Domain Applications
The programming skills you're learning in this lab can be applied in many fields beyond computer science.
In economics, the priority queue system you're building is similar to how [how financial markets process stock trades in order of importance](https://www.investopedia.com/articles/active-trading/101014/basics-algorithmic-trading-concepts-and-examples.asp) or how resources are efficiently distributed when they're limited.
Biologists use similar organizing structures to [map family trees of species](https://evolution.berkeley.edu/evolibrary/article/evo_05) and to process massive amounts of genetic data in a sensible order.
In chemistry, these concepts help [simulate how molecules interact over time](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2918236/) and organize different chemical compounds into related groups.
Math students utilize heap data structures like you're implementing to solve problems involving the [shortest path between points](https://medium.com/@josephcardillo/dijkstras-algorithm-and-google-maps-b6fc53b01c0a) and to model situations where events happen in sequence.
Even in sociology, these [programming patterns help researchers understand social networks](https://www.sciencedirect.com/topics/computer-science/social-network-analysis) (like friend groups), organize people into demographic categories, and make sense of complex survey responses.
By mastering these concepts, you're gaining skills valuable for interdisciplinary work where organizing data, processing things in order of priority, and modeling relationships between objects are important.

---

## Troubleshooting Common Issues

**Debugging Recommendation**: Test each test, one at a time. Use the `.info()` method to check the state of the data structure.

* `The autograder failed to execute correctly. Please ensure that your submission is valid. ...`

Remove the `print()` statements from your code or place them inside the `if __name__ == "__main__":` block to not interfere with the autograder tests. You want to make sure that the final product that you are delivering does not have any debugging statements when someone is importing your code for testing.

---

---

<sup>_* Acknowledgment: Original lab specifications are courtesy Richert Wang. This lab has been modified in collaboration with [Noah Spahn](https://github.com/noah-de) to incorporate cybersecurity context._</sup>
