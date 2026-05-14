---
layout: lab
num: lab06
ready: false
desc: "Prioritizing Cybersecurity Incidents"
assigned: 2026-05-14 11:00:00-7
due: 2026-05-19 23:59:59.59-7
---

# Introduction

In this lab, you'll have the opportunity to practice:

- Defining classes in Python
- Overloading the `==`, `<`, and `>` operators in a Python class
- Implementing an **O(n log n)** mergesort on a list of `Incident` objects
- Writing functions that ensure a list of incidents is in sorted order
- Determining the highest-priority / lowest-priority incident in a list
- Listing all incidents that can be resolved within a given time budget
- Merging two sorted linked lists into a new linked list
- Testing your functionality with `pytest`

For this lab, you are working with a university security operations team. During a busy week, analysts may receive many incident reports from different campuses and need to decide which incidents should be handled first. Your task is to create a program that sorts `Incident` objects according to a triage policy.

## Cybersecurity Context

This lab is inspired by the May 2026 nationwide security incident involving
Canvas. During that event, the University of California monitored updates from
Instructure and cybersecurity partners, temporarily restricted access in some
cases, and warned community members to remain vigilant about phishing and other
suspicious communications. In this lab, you will model a simplified version of
how a security team might prioritize incidents based on severity, user impact,
and estimated response effort.

Source: [UCnet: Nationwide security incident involving Canvas](https://ucnet.universityofcalifornia.edu/employee-news/nationwide-security-incident-involving-canvas/)

The three most important properties of an `Incident` are:

- its `severity`
- the number of `affected_users`
- its estimated `duration`

You will sort incidents as follows:

1. Higher severity incidents should come first.
2. If severity is tied, the incident affecting more users should come first.
3. If severity and affected users are tied, the incident with the shorter duration should come first.

You may assume that severity will always be one of `"low"`, `"med"`, or `"high"`.

## A Brief Note on Comparing Incidents

A common question for this lab is:

*If `incident0 < incident1` returns `True`, why does that mean `incident0` should be handled earlier?*

Recall that a sorted list places "smaller" elements first. In this lab, we define "smaller" to mean "higher priority in the sorted order." That is why `incident0 < incident1` should return `True` when `incident0` deserves earlier attention than `incident1`.

# Instructions

You will work with the following files:

- `incident.py` - file containing a class definition for an `Incident` object
- `lab06.py` - file containing mergesort and helper functions
- `linked_list.py` - file containing the linked-list merge portion of the lab
- `node.py` - helper file containing a linked-list node class
- `testFile.py` - file containing `pytest` functions testing the correctness of your code

Starter code is provided. You should keep all of these files in the same
directory so they can import each other cleanly.

# `incident.py` class

The `incident.py` file will contain the definition of an `Incident`. We will define the incident attributes as follows:

- `severity` - string representing the incident severity. This will be `"low"`, `"med"`, or `"high"`
- `affected_users` - integer representing how many users are impacted
- `duration` - float representing the estimated number of hours needed to resolve the incident

Write a constructor that allows the user to construct an incident object by passing in values for all fields. You may assume that all attributes will be defined, so there should be no default values.

- `__init__(self, severity, affected_users, duration)`

In addition to your constructor, your class definition should support getter methods:

- `get_severity(self)`
- `get_affected_users(self)`
- `get_duration(self)`

You will also implement:

- `get_incident_details(self)`

This method returns a `str` with all of the incident attributes in the following **exact** format. There is no trailing newline.

```python
i0 = Incident("high", 2400, 6.5)
print(i0.get_incident_details())
```

Output:

```text
(Incident) Severity: high, Affected Users: 2400, Duration: 6.5 hours
```

Lastly, your `Incident` class should overload the `>`, `<`, and `==` operators. These overloaded operators must follow the triage policy from the introduction:

- higher severity is higher priority
- if severity is tied, more affected users is higher priority
- if both are tied, shorter duration is higher priority
- if all three attributes are the same, neither incident is higher priority than the other

For example, `incident0 < incident1` should return `True` when `incident0` should appear earlier than `incident1` in the sorted list. If two incidents have the same severity, affected users, and duration, then both `incident0 < incident1` and `incident0 > incident1` should return `False`, while `incident0 == incident1` should return `True`.

# `lab06.py`

This file will contain functions that sort a list of `Incident` objects, check if the list is sorted correctly, retrieve the highest / lowest priority incidents, and list incidents that can be resolved within a time budget.

Function definitions must match exactly:

- `merge_sort(incidentList)` - performs a mergesort on a Python list of `Incident` objects using the ordering rules above. **Gradescope will test to ensure this list-based mergesort runs in `O(n log n)` time.**
- `ensure_sorted_ascending(incidentList)` - returns `True` if `incidentList` is sorted correctly from highest priority to lowest priority, and `False` otherwise
- `get_highest_priority_incident(incidentList)` - returns a string describing the highest-priority incident. Make use of `get_incident_details(self)` and `merge_sort(incidentList)`. You can assume `incidentList` has at least one incident.
- `get_lowest_priority_incident(incidentList)` - returns a string describing the lowest-priority incident. Make use of `get_incident_details(self)` and `merge_sort(incidentList)`. You can assume `incidentList` has at least one incident.
- `get_resolvable_incidents(incidentList, max_duration)` - returns a newline-separated string describing **all incidents whose duration is less than or equal to `max_duration`** from `incidentList` **in sorted order**. Make use of `get_incident_details(self)` and `merge_sort(incidentList)`.

Notes:

- You may assume `incidentList` has at least one incident.
- `get_resolvable_incidents` should return an empty string if there are no matching incidents.
- There should be no trailing newline at the end of the returned string.

## Note: Python Lists vs. Linked Lists

The first sorting portion of this lab uses a regular Python list of `Incident`
objects. The `merge_sort(incidentList)` function in `lab06.py` is a full
mergesort on that Python list.

The next portion switches data structures. In `linked_list.py`, you will work
with singly linked lists. This linked-list part does **not** ask you to write a
full linked-list mergesort. Instead, it asks you to merge two linked lists that
are already sorted.

This distinction matters for runtime. Merging two sorted linked lists can be
done by walking through both lists once and building the result as you go. That
takes `O(n + m)` time for linked lists of lengths `n` and `m`. If we tried to
merge sorted data by repeatedly inserting items into the middle of a Python
list, each insertion could require shifting many elements, making the total
runtime potentially quadratic.

# `linked_list.py`

This file contains the interview-style linked-list portion of the lab. The
provided `Node` class in `node.py` stores an `Incident` object in each node.
The provided `LinkedList` class in `linked_list.py` stores a reference to the
first node through `self.head`.

The starter code also includes:

- `append_incident(self, incident)` - appends an incident to the end of a linked list
- `print_list(self)` - prints each incident in the linked list from head to tail

You will implement:

- `merge_sorted_lists(list1, list2)` - takes two `LinkedList` objects whose incidents are already sorted according to the ordering rules from this lab and returns a new `LinkedList` containing all incidents in sorted order

Notes:

- You may assume both input linked lists are already sorted correctly.
- The merged linked list should preserve the same highest-priority-to-lowest-priority order used throughout the lab.
- This function is not a full linked-list mergesort. It only merges two linked lists that have already been sorted.
- A strong solution should run in `O(n + m)` time, where `n` and `m` are the lengths of the two linked lists.
- `merge_sorted_lists` should return a new linked-list structure made of new `Node` objects.
- You should not create any new `Incident` objects inside `merge_sorted_lists`.

# How to Best Approach This Lab

These functions rely on one another, so a bug in one place may cause tests elsewhere to fail. A suggested order is:

1. constructor and getters in `incident.py`
2. `get_incident_details` in `incident.py`
3. `<`, `>`, and `==` overloaded operators in `incident.py`
4. `ensure_sorted_ascending` in `lab06.py`
5. `merge_sort` in `lab06.py`
6. `get_highest_priority_incident` and `get_lowest_priority_incident` in `lab06.py`
7. `get_resolvable_incidents` in `lab06.py`
8. `merge_sorted_lists` in `linked_list.py`

# Sample Output 1

```python
from incident import Incident
from lab06 import ensure_sorted_ascending, merge_sort

i0 = Incident("low", 90, 1.5)
i1 = Incident("med", 90, 1.5)
i2 = Incident("med", 90, 4.0)
i3 = Incident("med", 250, 2.0)
i4 = Incident("high", 120, 3.0)
i5 = Incident("high", 120, 8.0)
incidentList = [i0, i1, i2, i3, i4, i5]

print("incidentList is NOT SORTED:")
for incident in incidentList:
    print(incident.get_incident_details())

assert ensure_sorted_ascending(incidentList) == False
merge_sort(incidentList)
assert ensure_sorted_ascending(incidentList) == True

print("incidentList is SORTED:")
for incident in incidentList:
    print(incident.get_incident_details())
```

Output:

```text
incidentList is NOT SORTED:
(Incident) Severity: low, Affected Users: 90, Duration: 1.5 hours
(Incident) Severity: med, Affected Users: 90, Duration: 1.5 hours
(Incident) Severity: med, Affected Users: 90, Duration: 4.0 hours
(Incident) Severity: med, Affected Users: 250, Duration: 2.0 hours
(Incident) Severity: high, Affected Users: 120, Duration: 3.0 hours
(Incident) Severity: high, Affected Users: 120, Duration: 8.0 hours
incidentList is SORTED:
(Incident) Severity: high, Affected Users: 120, Duration: 3.0 hours
(Incident) Severity: high, Affected Users: 120, Duration: 8.0 hours
(Incident) Severity: med, Affected Users: 250, Duration: 2.0 hours
(Incident) Severity: med, Affected Users: 90, Duration: 1.5 hours
(Incident) Severity: med, Affected Users: 90, Duration: 4.0 hours
(Incident) Severity: low, Affected Users: 90, Duration: 1.5 hours
```

# Sample Output 2

```python
from incident import Incident
from lab06 import get_highest_priority_incident, get_lowest_priority_incident

i0 = Incident("med", 200, 2.0)
i1 = Incident("high", 45, 0.5)
i2 = Incident("high", 45, 1.0)
i3 = Incident("med", 400, 8.0)
i4 = Incident("low", 1000, 12.0)
i5 = Incident("high", 800, 3.0)
incidentList = [i0, i1, i2, i3, i4, i5]

print("Highest Priority Incident:")
print(get_highest_priority_incident(incidentList))

print("Lowest Priority Incident:")
print(get_lowest_priority_incident(incidentList))
```

Output:

```text
Highest Priority Incident:
(Incident) Severity: high, Affected Users: 800, Duration: 3.0 hours
Lowest Priority Incident:
(Incident) Severity: low, Affected Users: 1000, Duration: 12.0 hours
```

# Sample Output 3

```python
from incident import Incident
from lab06 import get_resolvable_incidents

i0 = Incident("med", 180, 7.0)
i1 = Incident("high", 80, 2.5)
i2 = Incident("high", 80, 1.0)
i3 = Incident("low", 900, 0.5)
i4 = Incident("med", 300, 6.0)
i5 = Incident("high", 400, 9.0)
incidentList = [i0, i1, i2, i3, i4, i5]

print("Incidents resolvable within 6 hours:")
print(get_resolvable_incidents(incidentList, 6.0))
```

Output:

```text
Incidents resolvable within 6 hours:
(Incident) Severity: high, Affected Users: 80, Duration: 1.0 hours
(Incident) Severity: high, Affected Users: 80, Duration: 2.5 hours
(Incident) Severity: med, Affected Users: 300, Duration: 6.0 hours
(Incident) Severity: low, Affected Users: 900, Duration: 0.5 hours
```

# Sample Output 4

```python
from incident import Incident
from linked_list import LinkedList, merge_sorted_lists

list1 = LinkedList()
list1.append_incident(Incident("high", 300, 5.0))
list1.append_incident(Incident("med", 120, 2.0))

list2 = LinkedList()
list2.append_incident(Incident("high", 80, 3.0))
list2.append_incident(Incident("low", 900, 0.5))

merged = merge_sorted_lists(list1, list2)
merged.print_list()
```

Output:

```text
(Incident) Severity: high, Affected Users: 300, Duration: 5.0 hours
(Incident) Severity: high, Affected Users: 80, Duration: 3.0 hours
(Incident) Severity: med, Affected Users: 120, Duration: 2.0 hours
(Incident) Severity: low, Affected Users: 900, Duration: 0.5 hours
```

# Starter Code

Copy the following starter files into your Lab06 directory.

## `incident.py`

```python
class Incident:
    def __init__(self, severity, affected_users, duration):
        self.severity = severity
        self.affected_users = affected_users
        self.duration = duration

    def get_severity(self):
        return self.severity

    def get_affected_users(self):
        return self.affected_users

    def get_duration(self):
        return self.duration

    def get_incident_details(self):
        pass

    def __lt__(self, other):
        pass

    def __gt__(self, other):
        pass

    def __eq__(self, other):
        pass
```

## `lab06.py`

```python
def merge_sort(incidentList):
    pass


def ensure_sorted_ascending(incidentList):
    pass


def get_highest_priority_incident(incidentList):
    pass


def get_lowest_priority_incident(incidentList):
    pass


def get_resolvable_incidents(incidentList, max_duration):
    pass
```

## `node.py`

```python
class Node:
    def __init__(self, incident):
        self.incident = incident
        self.next = None
```

## `linked_list.py`

```python
from node import Node


class LinkedList:
    def __init__(self):
        self.head = None

    def append_incident(self, incident):
        new_node = Node(incident)

        if self.head is None:
            self.head = new_node
            return

        current = self.head
        while current.next is not None:
            current = current.next

        current.next = new_node

    def print_list(self):
        current = self.head

        while current is not None:
            print(current.incident.get_incident_details())
            current = current.next


def merge_sorted_lists(list1, list2):
    pass
```

## `main.py`

```python
from incident import Incident
from lab06 import (
    ensure_sorted_ascending,
    get_highest_priority_incident,
    get_resolvable_incidents,
    merge_sort,
)
from linked_list import LinkedList, merge_sorted_lists


def main():
    incident_list = [
        Incident("med", 180, 7.0),
        Incident("high", 80, 2.5),
        Incident("low", 900, 0.5),
        Incident("high", 400, 9.0),
    ]

    # Uncomment these lines after implementing the lab06.py functions.
    # print("Initially sorted?", ensure_sorted_ascending(incident_list))
    # merge_sort(incident_list)
    # print("Highest priority incident:")
    # print(get_highest_priority_incident(incident_list))
    # print("Resolvable within 6 hours:")
    # print(get_resolvable_incidents(incident_list, 6.0))

    list1 = LinkedList()
    list1.append_incident(Incident("high", 300, 5.0))
    list1.append_incident(Incident("med", 120, 2.0))

    list2 = LinkedList()
    list2.append_incident(Incident("high", 80, 3.0))
    list2.append_incident(Incident("low", 900, 0.5))

    # Uncomment these lines after implementing merge_sorted_lists.
    # print("Merged linked list:")
    # merged = merge_sorted_lists(list1, list2)
    # merged.print_list()

if __name__ == "__main__":
    main()
```

## `testFile.py`

```python
from incident import Incident
from linked_list import LinkedList, merge_sorted_lists
from lab06 import (
    ensure_sorted_ascending,
    get_highest_priority_incident,
    get_lowest_priority_incident,
    get_resolvable_incidents,
    merge_sort,
)


def test_incident_details():
    # TODO: create an Incident and test get_incident_details.
    pass


def test_comparison_operators():
    # TODO: test <, >, and ==.
    # Include a case where all three attributes are the same.
    pass


def test_merge_sort():
    # TODO: create an unsorted list, call merge_sort, and assert it is sorted.
    pass


def test_priority_helpers():
    # TODO: test get_highest_priority_incident and get_lowest_priority_incident.
    pass


def test_resolvable_incidents():
    # TODO: test get_resolvable_incidents with matching and non-matching incidents.
    pass


def test_merge_sorted_lists():
    # TODO: create two sorted linked lists, merge them, and assert the merged order.
    pass
```

# `testFile.py` pytest

This file should import your `Incident` class, your `lab06.py` functions, and
your linked-list merge function. Write `pytest` tests that check the correctness
of your class and functions. You should write at least one test for each method
in these files, including the overloaded operators, but excluding the getters.

# Submission

Once you finish, submit your `incident.py`, `lab06.py`, `node.py`,
`linked_list.py`, and `testFile.py` files to the Lab06 Gradescope assignment.
The autograder will run unit tests to ensure your code matches the
specification above. There will also be tests to ensure that your mergesort
implementation in `lab06.py` runs in **O(n log n)** time. The linked-list
merge portion is expected to run in **O(n + m)** time because the two input
linked lists are already sorted.

---

*Acknowledgment: This lab has been modified in collaboration with [Sophia Pu](https://sophiapx.github.io/) and [Jingbo Yang](https://kimperyang.github.io/) to incorporate cybersecurity context.*
