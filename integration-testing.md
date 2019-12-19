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
jest.mock('@/router');

import { mount } from '@vue/test-utils';
import { initLocalVue } from '../utils';
import SeminarEventDetail from '@/views/seminars/seminar-event-detail.vue';
import { i18n } from '@/lang';
import Vuex from 'vuex';
import staff from '@/store/modules/staff';
import members from '@/store/modules/members';

describe('SeminarEventDetail', () => {

    const localVue = initLocalVue();

    const mountOptions = {
        localVue,
        i18n,
        store: new Vuex.Store({
            modules: {
                staff,
                members,
            },
        }),
        mocks: {
            $route: {
                params: {},
                query: {},
            }
        }
    };

    it('should display the correct output', () => {
        const wrapper = mount(SeminarEventDetail, mountOptions);
        expect(wrapper).toMatchSnapshot();
    });

    it('should render children', () => {
        const wrapper = mount(SeminarEventDetail, mountOptions);
        const timePicker = wrapper.find({ name: 'time-picker' });
        const datePicker = wrapper.find({ name: 'date-picker' });
        expect(timePicker.contains('select.custom-select')).toBe(true);
        expect(datePicker.contains('[placeholder="Select a date"]')).toBe(true);
    });

    it('should not contain email field', () => {
        const wrapper = mount(SeminarEventDetail, mountOptions);
        expect(wrapper.contains('input[type="email"]')).toBe(false);
    });

    it('should contain email field', () => {
        const wrapper = mount(SeminarEventDetail, {
            ...mountOptions,
            mocks: {
                $route: {
                    params: { id: '123' },
                    query: {},
                }
            }
        });
        expect(wrapper).toMatchSnapshot();
        expect(wrapper.contains('input[type="email"]')).toBe(true);
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

Check out the [unit testing guide](/) for the coding standards and best practices for writing integration tests.



