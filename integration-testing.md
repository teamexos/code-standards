## EXOS // Integration Testing Guide



### Integration Tests

#### What are they?

Integration tests are very similar to unit tests but instead of testing a single component it tests the interactions of multiple components at together. Because of their similarity to unit tests, integration tests are written and ran using the same framework and commands as unit tests. While integration tests can be written for any type of code we will focus mainly on writing tests for components. 

#### When should I write an integration test?

As a rule, at EXOS, at the very least **all route-level components should have an accompanying integration test**. Other conditions when an integration test may be necessary are:

- A component contains child components that communicate with one another in a way that is different then the standard top-down communication handled by props (e.g., sibling-to-sibling, child-to-parent, etc.). 
- A component containing child components that aren't testable from a route-level component test (e.g., tabbed content, modals, collapsed/hidden content, etc.).
- A function that accepts arguments, runs sub-routines, applies non-trivial logic to its inputs, and returns an output (e.g., ). 



#### How do I write an integration test?

#### What types of things should I assert?

Snapshot assertions, call counts for stubbed functions, inputs change outputs... research more

#### Which dependencies should I allow and which should I stub/mock?

All API calls should be mocked, route if query/url params are referenced ... research more. 

#### What's our standards and best practices for writing integration tests?

Check out the [unit testing guide](/) for our coding standards and best practices writing integration tests.