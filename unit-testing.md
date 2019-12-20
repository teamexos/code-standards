# EXOS  //  Unit Testing Guide



## Unit Tests



### What are they?

Unit testing is the process of running tests against the smallest parts of an application (units).  Normally, the units you test are functions, but in Vue apps, components are also units to test.

Put simply, unit tests are functions that call functions in your source code in isolation and assert that they behave correctly.  Since unit tests run against an isolated unit, when a good unit test fails, it should point you directly toward the problem in the code.  Also, good unit tests can serve as developer documentation regarding how a component should work.

### When should I write a unit test?

As a rule, at EXOS, you should write unit tests for every non-routed component.

### How do I write a unit test?

We should write tests that test a component in isolation, which means they should be shallow mounted.  The tests should be focused on testing the component contract.

### Best Practices

- Start by first by writing an overview of what the component is supposed to do.
- Use the [Vue Test Utils library](https://github.com/vuejs/vue-test-utils) and refer to the [documentation](https://vue-test-utils.vuejs.org/) on that site.
- Shallow mount component
- Component contract must be tested
    - Component inputs
        - Component props
        - User actions
        - Vue.js events
        - Vuex interactions with getters and state
    - Component outputs
        - Emitted events
        - Rendered output
            - Include snapshot tests for components
                - This can serve as verification of proper rendering
                - For dynamic components, remember to mock all inputs to make your tests deterministic
        - Function call
            - When a component's output is to call an external function, use a spy to verify that the function was called
        - Vuex interactions with actions or mutations
- When testing component output
    - Test only output that is dynamically generated
    - Test only output that is a part of the component contract
- Implementation details should not be covered
    - This means not covering private methods
    - Not testing implementation details makes unit tests more useful for facilitating code refactoring
- Tests should be written in organized and readable manner, so that they can serve as developer documentation
    - Unit tests make pull requests more reviewable
    - Unlike with separate documentation, a developer should be to rely upon unit tests as documentation with confidence
- If a module has a manual mock created (in a `__mocks__` folder local to the module) you must opt-in to use it at the top of your test (see [integration test example](integration-testing.md)). Since jest hoists all jest.mock() functions to the top of the file it is best practice to define them there to avoid confusion.

#### Snapshot Testing
Snapshot testing saves a lot of time and alleviates the need to assert the existence of most rendered elements, however, there is a risk where in the event of a failed build, someone can update snapshots and commit them for review without verifying that the snapshots are actually correct. Other reviewers may mistakenly approve those PRs and as a result introduce bad code into staging or production. To mitigate this risk, developers should always include a couple of assertions with their snapshot tests to verify that the DOM is indeed rendered correctly.

#### Mocking
Jest has a plethora of tools for mocking dependencies to help isolate component(s) you want to test. However, here a few quick tips to cut through the noise:

* To mock a dependency, create a folder called `__mocks__` at the same level where the dependency lives in the file tree. Inside that folder create a file with the same filename as the dependecy. You may mix the real depenncy with your own changes. For instance:

```javascript
const bootstrap = jest.requireActual('../bootstrap');

const state = {
    ...bootstrap.default.state,
    loadedCH: true,
};

export default {
    ...bootstrap.default,
    state,
};
```

* To use your mocks you must opt-in at the top of your test:
```javascript
jest.mock('@/store/bootstrap');

import { mount } from '@vue/test-utils';
import { getMountOptions, getStoreModules } from '../utils';
import SeminarManager from '@/views/seminars/seminar-manager.vue';

describe('SeminarManager', () => {
...
```

* If you've created a mock that you want enabled for ALL tests opt-in in here: `tests/test-env.js`

* `jest.mock()` mocks that file for the entirety of the test file. If you want to mock a file for just a single test within a spec then use `jest.doMock()` inside your test block. This function is not hoisted to the top of the file but enables the mock just for that test.

* If you want load up a vuex store module with a specific changes (other than what is set in the `__mocks__` folder) you can use the `getStoreModules` helper utility to achieve this. By default this method will return all store modules (mocked if you opted in or the actual module) for you to then pass on to the `getMountOptions({ modules })` for initializing the store with. However, you can pass `getStoreModules` arguments to update specific parts of the module tree like so:

```javascript
const clientsStore = jest.requireActual('@/store/modules/account/clients').default;
const modules = getStoreModules([
    ['bootstrap.state', { loadedCH: false }],
    ['account.modules.clients.state', clientsStore.state],
]);
const mountOptions = getMountOptions({
    modules
});
const wrapper = mount(SeminarManager, mountOptions);
```


#### Stubbing
If you are testing a component that has a child component you don't want to test (but you want to test other child components) you may want to stub out the child component so it's not rendered. You can use the `getMountOptions` helper utility to generate your options like so:

```javascript
import { getMountOptions } from '../utils';
import SeminarManager from '@/views/seminars/seminar-manager.vue';

describe('SeminarManager', () => {

    const mountOptions = getMountOptions({
        stubs: ['router-link']
    });

    const wrapper = mount(SeminarManager, mountOptions);
    ...

```

In the example above, all `<router-link />` components will not be rendered.


### Full example of unit test:

```javascript
import { shallowMount, text } from '@vue/test-utils'
import AssessmentProgress from '@/views/assessment/common/AssessmentProgress.vue';
import config from '@/config';

const sections = [
    {
        title: 'Basics',
        pages: [
            { reEval: false, slug: 'start'},
            { reVal: false, slug: 'intro' },
            { slug: 'measurements' },
        ]
    },
    {
        title: 'Mindset',
        pages: [
            { slug: 'intro' },
            { slug: 'feeling '},
            { slug: 'stress' },
        ]
    },
    {
        title: 'Nutrition',
        pages: [
            { slug: 'intro' },
            { slug: 'foods' }
        ]
    },
];

describe('AssessmentProgress.vue', () => {
    test('It should render correctly', () => {
        expect(shallowMount(AssessmentProgress, {
            propsData: {
                sections: [],
                current: [0, 0]
            }
        }))
        .toMatchSnapshot();
    });
    test('It should mount even with falsy values', () => {
        const wrapper = shallowMount(AssessmentProgress, {
            propsData: {
                sections: [],
                current: [0, 0],
            },
        });
        expect(wrapper.isVueInstance()).toBeTruthy();
    });
    test('It should display the section title', () => {
       const wrapper = shallowMount(AssessmentProgress, {
           propsData: {
               sections,
               current: [0, 1],
           },
       });
       expect(wrapper.text()).toContain('Basics');
    });
    test('It should render a progress bar element', () => {
        const wrapper = shallowMount(AssessmentProgress, {
            propsData: {
                sections,
                current: [0, 0],
            }
        });
        expect(wrapper.contains('.progress-bar')).toBe(true);
    });
    test('It should show progress to be 0% when on first section', () => {
        const wrapper = shallowMount(AssessmentProgress, {
            propsData: {
                sections,
                current: [0, 0],
            }
        });
        expect(wrapper.find('.completed').attributes().style).toBe('width: 0%;');
    });
});
```
### Articles and reference on Vue testing:

Official docs and cookbook info:
- https://vuejs.org/v2/guide/unit-testing.html
- https://vuejs.org/v2/cookbook/unit-testing-vue-components.html

Vuex also has documentation on testing:
- https://vue-test-utils.vuejs.org/guides/using-with-vuex.html

More info that could be useful:
- A bit old but Laracasts always has good gems: https://laracasts.com/series/testing-vue
- Vue mastery seems to have a unit testing course coming up: https://www.vuemastery.com/courses



