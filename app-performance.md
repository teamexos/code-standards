# EXOS  //  Application Performance Guidelines


## Overview

 *A study shows that within 200 milliseconds to 1 second, people feel that they are within their flow of action. After 1 second without any feedback, they are most likely to lose their focus and after 10 seconds, users focus is likely to be lost entirely.*

Our goals:

- Ensure great performance in terms of design and technical implementation
- Understand the expectations of the potential users and provide a great experience in terms of waiting time  


## Best practices 

### Response time
- The application rendering time should be under the 1 second mark as much as possible. Exceptions must be discussed with the development team. 
- API implementations must consider this expectation. A [guideline](https://github.com/teamexos/backend-code-standards/blob/master/sections/9.0%20api.rst) is in place so good endpoint performance is enforced.    

### Progress indicators

#### Avoid usage of loaders or spinners
- Loader or spinners create a period of uncertainty for the user since the load time is unknown. It draws attention to the fact that the user is waiting.
 
#### What is a good progress indicator?
- Gives immediate feedback.
- Provides a sense of time (how much has progressed, and how much is pending).
- Removes doubt (gradual progress reassures people that the app is working).

#### Progressive loading with Skeleton Screens
- In progressive loading an individual element becomes visible as soon as it is loaded, it helps in displaying the content that is exactly loaded and what is yet to be loaded.
- Skeleton screen is a low fidelity UI into which information is gradually loaded. It gives users a visual cue that the content is being loaded into each UI element.
- While implementing progressive loading in the skeleton screen we should keep in mind the goal we are trying achieve with the product and prioritize the loading content accordingly.

#### When to use progressive loading?
- In general we should try to use a non blocking progressive approach whenever possible.
- Implementation is required for cases where we can foresee that the response data may vary based on different factors. 
  
 

