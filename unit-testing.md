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
    
Example:

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



