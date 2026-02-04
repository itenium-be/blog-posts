
This is probably a few blog posts and not one big one
--> Lots of information on the UnitTesting itenium session...


Sociabel test (email) with SociabelTesting.zip




Why I don't have ParameterizedTests in the "Testing Frameworks" comparison.
I don't like em.

They have their uses alright.
--> many inputs that are in the same Equivalence Partitioning
--> ex: tests for latinize
  - NullInput_ReturnsNull
  - EmptyString_ReturnsEmptyString
  - WeIrDCaSiNG_KeepsTheCasing
  - FrenchEees_AreConvertedToNormalEees (éêè -> eee)
  - GermanUmlaut_IsConvertedToC
  - Numbers_AreNotConverted


But they are misused so often.
--> they provide the inputs and the outputs
--> So for the example above --> put all inputs & outputs in one test



Rant against using jest.each | lookup same in java/xUnit
--> No messages: which one failed is typically something your test runner can tell you; but why?
--> when you start debugging the test, you probably want to put all the other tests in comment?
--> so now you are changing code but you don't see if any of the other tests are failing
--> used for running some arbitrary inputs: if you checked the happy path and the edge cases, this is overkill, no new branches are being tested
--> if it's used for running different business use cases, those are probably better off being separate test cases
--> Talk a little about "Boundary Analysis" & "Equivalence Partioning"?
--> How these tests often don't bring anything to the table regarding Equivalence Partioning?










My proposition was:
- I/O == Integration Test
- No I/O == UnitTest

https://en.wikipedia.org/wiki/Unit_testing
--> Even dubious in the Wikipedia article...

## Martin Fowler

https://martinfowler.com/bliki/UnitTest.html

What is a UnitTest
- low-level, focusing on a small part of the software system
- written by the programmers themselves
- expected to be significantly faster than other kinds of tests

Differences: **What people consider to be a unit**.

Object-oriented design tends to treat a class as the unit
But really it's a situational thing - the team decides what makes sense to be a unit for
the purposes of their understanding of the system and its testing.

Although I start with the notion of the unit being a class,
I often take a bunch of closely related classes and treat them as a single unit.

However you define it doesn't really matter.

### Classic and mockist styles

https://martinfowler.com/articles/mocksArentStubs.html

- Classic == sociable: test groups of related classes, mocking only IO, 3rd Party etc
- Mockist == solitary: test in isolation, mocking everything

Other links:

https://stackoverflow.com/questions/38181/when-should-i-mock

https://www.artofunittesting.com/definition-of-a-unit-test#:~:text=A%20unit%20of%20work%20can,achieve%20this%20isolation%20when%20needed

https://softwareengineering.stackexchange.com/questions/400266/is-testing-behavior-of-many-classes-in-one-test-still-unit-testing



----------------------------------------


- Geen UnitTesten: Proberen kaderen? Gebruik een nieuw stuk code of een bugfix/change op een bestaand stuk dat veel "rare" business logic bevat om de eerste testen te schrijven. Je weet zelf heel goed dat voor dingen die veel logica bevatten het vaak sneller kan ontwikkeld worden door ook testen te schrijven. 
- Beter to ask for forgiveness than for permission (tenzij ze heel vijandig tegenover UnitTesting staan?
- Geen budget / tijd voor testen: Wat ze zeker niet willen dat het op tijd is maar niet correct werkt. Zeker als het in productie staat. Iedereen wil uiteindelijk kwaliteit, en nieuw development deftig testen is onderdeel van het ontwikkelproces. Of jij nu manueel test of via UnitTests, als je (veel) minder bugs oplevert dan de rest van het team dan gaat dat opvallen. Lead by example?
Blog post? Couldn't use git at a client (no branching, ...). I used it anyway (.git folder in tfsignore). The client was not affected since it was all locally but I was able to have greater control over commits: One big TFS commit per story could be many many git commits.
Boy Scout Rule: Leave the playground a little bit cleaner than you found it.
Working Effectively with Legacy Code


-----

Some emails:

Code Coverage vs Branch Coverage

Code Coverage / Statement Coverage:
The percentage of source code that is executed by the tests. It is useful to identify which parts of the code are untested and might contain undetected bugs. It is easy to calculate and is one of the metrics of SonarQube.

Branch Coverage / Decision Coverage:
To have complete “Branch Coverage” all possible routes through the code need to be tested. This reveals bugs that 100% Code Coverage alone cannot and ensures that all possible scenarios are covered by UnitTests.

It is therefor important to create all the UnitTests that are needed to get to complete Branch Coverage.

Example

Given the following method:

if (condition) {

} else {

}

if (condition2) {

} else {

}

For 100% Code Coverage, we need 2 UnitTests, for example
Test1: condition1 & condition2 are true
Test2: condition1 & condition2 are false
For 100% Branch Coverage, we need 4 UnitTests, for example
Test1: condition1 & condition2 are true
Test2: condition1 & condition2 are false
Test3: condition1 is true & condition2 is false
Test4: condition1 is false & condition2 is true
Example 2

if (cond1 && (cond2 || cond3)) {

}

To attain a good Branch Coverage it’s best to have a test that covers these cases:

cond1 is false
cond1 is true and cond2/3 are false
cond1/2 are true and cond3 is false
cond1/3 are true and cond2 is false
When cond1 is already false, you could still cover all different cases for cond2/3 but, it doesn’t really matter since they are not being evaluated. Because these are white box tests, this is OK. If this is somewhat tricky/important code, subject to change, or in a part of the code in which bugs have been previously encountered, you may still want to write a test for all cases.

State Testing vs Behavior Testing

State Testing:
Given a certain input, check that the correct output is produced. Examples:

When doing a calculation: assert that the response is correct
When retrieving data with a Request object: assert that the correct records are returned
When the database is updated: assert that the entities are in the expected end state
Behavior Testing:
Given a certain input, check that certain object interactions did or did not happen. Typically used when dealing with external systems. Examples:

When doing a calculation: verify that an emergency is raised when the calculation fails. Also verify that no emergency is being raised when the calculation succeeded!
When retrieving data with a Request object: verify that an email is sent when data is requested that is not available (but should’ve been)
When the database is updated: verify that the DbContext.Save method is called
Equivalence Partitioning and Boundary Value Analysis

Simple case: we expect an EnergyAmount to be between 0 and 100.

Equivalence Partitioning:
With the following 3 tests:

An invalid low value <0 (example: -10)
A correct value (example: 20)
An invalid high value >100 (example: 200)
We have partitioned the data into 3 sections. It is not needed to create additional low/correct/high values since they all belong to the same 3 partitions we already covered.

Boundary Value Analysis:
There are only two hard problems in Computer Science: cache invalidation, naming things, and off-by-one errors.

This is where Boundary Value Analysis comes into play. Instead of just checking our semi-random values for the Equivalence Partitioning, we might add (or replace) them with boundary values to ensure that we haven’t made any accidental off-by-one errors.
We could add the following test cases:

Values -1 and 0
Values 100 and 101
Edge Case Testing:
This could be considered a case of Boundary Value Analysis. In the EnergyAmount example, also create a test using NULL as input. You could also define the behavior of PositiveInfinity, NegativeInfinity and NaN EnergyAmounts – if this makes sense for your UseCase!

----

Other email:
Maybe check the UnitTesting with examples in .NET book?

 

Seams à working effectively with legacy code?

 

Happy Path tests

 

TDD or no TDD

Useless tests (IOC registration, setup/infrastructure code)
At least the tests are written
 

AAA vs GWT

Multiple Asserts per Test?
 

State vs Behavior Testing

 

Naming of tests

 

Readability of tests

Builders
Base classes
 

Code/Statement Coverage vs Branch Coverage

if () {

    var dao = _factory.Something()

    _repo.Save(dao);

} else {

 

}

 

if (x >= 5) {

    // .Not.Received()

} else {

    _emailService.SendEmail();

}




return result;

-------




