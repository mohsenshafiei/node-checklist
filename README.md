### node-checklist

##### Structure

* Structure you solution by components
  - The ultimate solution is to develop small software: devide the whole stack into self-contained components that don't share files with others
  - The very least you should do is create basic borders between components, assign a folder in your project root for each business component and make it self-contained.
  - Other components are allowed to consume its functionality only through its public interface or API.
  - Structure your solution by self-contained components
```bash
      orders/
      products/
      users/
      |---- index.js
      |---- user.js
      |---- userAPI.js
      |---- usersController.js
      |---- usersDAL.js
      |---- usersErrors.js
      |---- usersServices.js
      |---- userTesting.js
```
  - Group your files by technical role
  ```bash
    controllers/
      |---- api.js
      |---- home.js
      |---- order.js
      |---- product.js
      |---- user.js
    models/
      |---- order.js
      |---- product.js
      |---- user.js
    test/
      |---- testOrder.js
      |---- testProduct.js
      |---- testUser.js
    utils/
    views/
      |---- contact.pug
      |---- home.pug
      |---- layout.pug
  ```

* Seperate component code into layers: web, services, and DAL
* Keep express in the web layer only
* API developers tend to mix layers by passing the web layer objects (Express req, res) to business logic and data layers - this makes your application dependant on and accessible by Express only
* Sharing your own common utilities across environments and components
* API Declaration, should be reside in app.js
* Server network declaration, should reside in /bin/www
* A reliable config solution must combine both configuration files + overrides from the process variables.
* use hierarchical JSON file for config file


##### Error Handling Practices

- Callbacks don’t scale well since most programmers are not familiar with them.
- Callbacks do something even more sinister: they deprive us of the stack, which is something we usually take for granted in programming languages.
- Promises are native ES6, can be used with generators, and ES7 proposals like async/await through compilers like Babel.
- Using only the built-in Error object will increase uniformity and prevent loss of information.
- When invoking some component, being uncertain which type of errors come in return – it makes proper error handling much harder.
- Passing a string instead of an error results in reduced interoperability between modules
- Inheriting from Error doesn’t add too much value
- All JavaScript and System errors raised by Node.js inherit from Error
- Distinguish operational vs programmer errors
- Operational errors refer to situations where you understand what happened and the impact of it – for example, a query to some HTTP service failed due to connection problem. On the other hand, programmer errors refer to cases where you have no idea why and sometimes where an error came from – it might be some code that tried to read an undefined value or DB connection pool that leaks memory.
- The best way to recover from programmer errors is to crash immediately. You should run your programs using a restarter that will automatically restart the program in the event of a crash.
- The safest way to respond to a thrown error is to shut down the process. Of course, in a normal web server, you might have many connections open, and it is not reasonable to abruptly shut those down because an error was triggered by someone else. The better approach is to send an error response to the request that triggered the error while letting the others finish in their normal time, and stop listening for new requests in that worker.
-  Unless you really know what you are doing, you should perform a graceful restart of your service after receiving an “uncaughtException” exception event. Otherwise, you risk the state of your application, or that of 3rd party libraries to become inconsistent, leading to all kinds of crazy bugs
* There are primarily three schools of thoughts on error handling:
  - Let the application crash and restart it.
  - Handle all possible errors and never crash.
  - A balanced approach between the two
- Error handling logic such as mail to admin and logging should be encapsulated in a dedicated and centralized object that all endpoints (e.g. Express middleware, cron jobs, unit-testing) call when an error comes in
- Anti Pattern: handling errors within the middleware
- Note that it’s a common, yet wrong, practice to handle errors within Express middleware – doing so will not cover errors that are thrown in non-web interfaces.
- Handling each err individually would result in a tremendous amount of code duplication. The next best thing you can do is to delegate all error handling logic to an Express middleware
- Don’t cross the streams: HTTP errors have no place in your database code.
-  Let your API callers know which errors might come in return so they can handle these thoughtfully without crashing. This is usually done with REST API documentation frameworks like Swagger
- You have to tell your callers what errors can happen
- We’ve talked about how to handle errors, but when you’re writing a new function, how do you deliver errors to the code that called your function? …If you don’t know what errors can happen or don’t know what they mean, then your program cannot be correct except by accident. So if you’re writing a new function, you have to tell your callers what errors can happen and what they mean
- When an unknown error occurs (a developer error, see best practice number #3)- there is uncertainty about the application healthiness. A common practice suggests restarting the process carefully using a ‘restarter’ tool like Forever and PM2
- The best way to recover from programmer errors is to crash immediately
- Use a mature logger to increase error visibility
- We all love console.log but obviously, a reputable and persistent logger like `Winston`, `Bunyan` (highly popular) or `Pino` (the new kid in town which is focused on performance) is mandatory for serious projects.
- Whether professional automated QA or plain manual developer testing – Ensure that your code not only satisfies positive scenario but also handle and return the right errors.
- Without testing, whether automatically or manually, you can’t rely on our code to return the right errors. Without meaningful errors – there’s no error handling
* APM products constitute 3 major segments:
  - Website or API monitoring – external services that constantly monitor uptime and performance via HTTP requests. Can be set up in few minutes. Following are few selected contenders: `Pingdom`, `Uptime Robot`, and `New Relic`
  - Code instrumentation – product family which requires embedding an agent within the application to use features like slow code detection, exception statistics, performance monitoring and many more. Following are few selected contenders: New Relic, App Dynamics
  - Operational intelligence dashboard – this line of products is focused on facilitating the ops team with metrics and curated content that helps to easily stay on top of application performance. This usually involves aggregating multiple sources of information (application logs, DB logs, servers log, etc) and upfront dashboard design work. Following are few selected contenders: `Datadog`, `Splunk`, `Zabbix`
- never forget adding .catch clauses within each promise chain call and redirect to a centralized error handler.
- we should design things in such a way that mistakes hurt as little as possible, and that means handling errors by default, not discarding them.
- Fail fast, validate arguments using a dedicated library
- The validation code is usually tedious unless you are using a very cool helper library like `Joi`
- To do this, we recommend validating the types of all arguments at the start of the function.

##### Code Style
- Use `ESLint`
-  On top of ESLint standard rules that cover vanilla JS only, add Node-specific plugins like `eslint-plugin-node`, `eslint-plugin-mocha` and `eslint-plugin-node-security`
- The opening curly braces of a code block should be in the same line of the opening statement
- Don't Forget the Semicolon
- Name Your Functions
- Use `lowerCamelCase` when naming constants, variables and functions and `UpperCamelCase` (capital first letter as well) when naming classes.
- Prefer const over let. Ditch the var
- Requires come first, and not inside functions
- Do Require on the folders, not directly on the files
- Use the `===` operator
- Node 8 LTS now has full support for Async-await. This is a new way of dealing with asynchronous code which supersedes callbacks and promises. Async-await is non-blocking, and it makes asynchronous code look synchronous.
- Use Fat (=>) Arrow Functions

##### Testing And Overall Quality Practices
- At the very least, write API (component) testing
- Use a code linter to check basic quality and detect anti-patterns early. Run it before any test and add it as a pre-commit git-hook to minimize the time needed to review and correct any issue.
- Carefully choose your CI platform (Jenkins vs CircleCI vs Travis vs Rest of the world)
- Even the most reputable dependencies such as Express have known vulnerabilities. This can get easily tamed using community and commercial tools such as link `npm audit` and link `snyk.io` that can be invoked from your CI on every build
- Different tests must run on different scenarios: quick smoke, IO-less, tests should run when a developer saves or commits a file, full end-to-end tests usually run when a new pull request is submitted, etc.
- Code coverage tools like `Istanbul/NYC` are great for 3 reasons: it comes for free (no effort is required to benefit this reports), it helps to identify a decrease in testing coverage, and last but not least it highlights testing mismatches: by looking at colored code coverage reports you may notice, for example, code areas that are never tested like catch clauses (meaning that tests only invoke the happy paths and not how the app behaves on errors). Set it to fail builds if the coverage falls under a certain threshold
- Inspect for outdated packages
- Use docker-compose for e2e testing
- Refactor regularly using static analysis tools like `Sonarqube` and `Code Climate`
