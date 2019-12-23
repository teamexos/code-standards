# EXOS  //  Integration Testing Guide



## Integration Tests



### What are they?

Integration tests are very similar to unit tests but instead of testing a single component it tests the interactions of multiple components together. Because of their similarity to unit tests, integration tests are written and ran using the same framework and commands as unit tests. While integration tests can be written for any type of code we will focus mainly on writing tests for components.



### When should I write an integration test?

As a rule, at EXOS, at the very least **all route-level components should have an accompanying integration test**. Other conditions when an integration test may be necessary are:

- A component contains child components that communicate with one another in a way that is different then the standard top-down communication handled by props (e.g., sibling-to-sibling, child-to-parent, etc.).
- A component containing child components that aren't testable from a route-level component test (e.g., tabbed content, modals, collapsed/hidden content, etc.).
- A function that accepts arguments, runs sub-routines, applies non-trivial logic to its inputs, and returns an output (e.g., a function that calculates shopping cart totals).



### How do I write an integration test?

Writing a integration test is nearly identical to writing a unit test. The main difference is the child components of the component you are testing are not stubbed. So while a unit test may "shallow mount" the component it wants to test, an integration test will just "mount" the component, allowing its children to be rendered and included in what's being tested.

Example:

```javascript
// Opt-in to mocks at the beginning of the file (no matter where you put them in the test, jest will hoist him here anyway)
jest.mock('@/router');
jest.mock('@/store/modules/members/seminars');
jest.mock('@/store/modules/members/seminars/categories');
jest.mock('@/store/modules/members/seminars/registrations');
jest.mock('@/store/modules/members/seminars/events');
jest.mock('@/store/modules/account/clients');
jest.mock('@/store/modules/account/auth');

import { mount } from '@vue/test-utils';
import { getMountOptions, getStoreModules } from '../utils';
import SeminarManager from '@/views/seminars/seminar-manager.vue';

describe('SeminarManager', () => {

    // Define shared resources here rather then per test
    // Below grabs a reference to the mocked stores so we can verify actions are being called in our tests.
    const seminarCategoryStore = require('@/store/modules/members/seminars/categories').default;
    const seminarsStore = require('@/store/modules/members/seminars').default;

    const mountOptions = getMountOptions({
        created: function () {
            this.isLoading = false;
        },
        stubs: ['router-link']
    });

    const wrapper = mount(SeminarManager, mountOptions);

    it('should display the correct output when there is data', () => {
        expect(wrapper).toMatchSnapshot();
        // Anytime we verify a snapshot we must also verify at least one element that should exist (this ensures we do not mistakenly approve PRs that have snapshots of the wrong state)
        expect(wrapper.contains('.card-body input.form-control')).toBe(true);
    });

    // Along with snapshot tests of the component with and without data we should write a test to verify any state changes that might happen inside the component or its children.
    it('should be able to add category', () => {
        expect(seminarCategoryStore.actions.add.mock.calls.length).toBe(0);
        wrapper.type('.card-body input.form-control', 'New Category');
        wrapper.click('.card-body .form-inline button.btn-secondary');
        // Verify store actions have been called the correct number of times
        expect(seminarCategoryStore.actions.add.mock.calls.length).toBe(1);
        // Verify store actions have been called with the correct arguments
        expect(seminarCategoryStore.actions.add.mock.calls[0][1].name).toBe('New Category');
    });

    // We should also verify components work without any data.
    it('should display the correct output when there is no data', () => {
        const semStore = jest.requireActual('@/store/modules/members/seminars').default;
        const semCatStore = jest.requireActual('@/store/modules/members/seminars/categories').default;
        const semRegStore = jest.requireActual('@/store/modules/members/seminars/registrations').default;
        const semEveStore = jest.requireActual('@/store/modules/members/seminars/events').default;
        const clientsStore = jest.requireActual('@/store/modules/account/clients').default;
        const authStore = jest.requireActual('@/store/modules/account/auth').default;

        // Reset module states to their default states.
        const modules = getStoreModules([
            ['account.modules.clients.state', clientsStore.state],
            ['account.modules.auth.state', authStore.state],
            ['members.modules.seminars.state', semStore.state],
            ['members.modules.seminars.modules.categories.state', semCatStore.state],
            ['members.modules.seminars.modules.registrations.state', semRegStore.state],
            ['members.modules.seminars.modules.events.state', semEveStore.state],
        ]);
        const mountOptions2 = getMountOptions({
            modules
        });
        const wrapper = mount(SeminarManager, mountOptions2);
        expect(wrapper).toMatchSnapshot();
        // Again anytime we run a snapshot test we should also add 1 or more assertions to verify that the snapshot is indeed correct.
        expect(wrapper.findAll('.skeleton').length).toBe(1);
        expect(wrapper.contains('.card-body input.form-control')).toBe(false);
    });
});
```



### What types of things should I assert?

Some assertions your integration test may want to validate are:

- Did the all the components render?
- Does the outputted markup match the stored snapshot?
- Are computed values being shown as expected?
- Where methods called the correct number of times?
- Do methods return expected values?
- If there is communication between components, does that work as expected?



### What dependencies should I allow and which should I stub/mock?

All API results should be mocked, and if query/url params are referenced then you should also mock the route object.

The following items can be mocked but are not required: vuex modules, router, utility functions, etc.



### What's our standards and best practices for writing integration tests?

Check out the [unit testing guide](/) for our coding standards and best practices for writing integration tests.



