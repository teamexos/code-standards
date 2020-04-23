## Debugging

Read the callstack carefully. Dont rush to google the last error reported. Understand where the error is originating. Is it in our app or a dependency or a dependency of a dependency?

### Layout Issues

Less is more. Delete elements from the DOM to see at what point the layout issue is fixed. Then you know which component/element is causing the layout issue.

## Local development

To develop for any of the instances using the local server but not the local docker installation, the verbose version of the serve command is available in the README file in most projects (e.g. `APP_ENV=detect yarn serve --host=exos-qa.exos.biz --https --port 3000`). This will adjust the client's API services run the application in https://exos-qa.exos.biz:3000. 
