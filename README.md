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

##### Going to production

- Monitoring
- Increase transparency using smart logging
- Kibana (part of the Elastic stack) facilitates advanced searching on log content
- Kibana (part of the Elastic stack) visualizes data based on logs
- It’s very tempting to cargo-cult Express and use its rich middleware offering for networking related tasks like serving static files, gzip encoding, throttling requests, SSL termination, etc. This is a performance kill due to its single threaded model which will keep the CPU busy for long periods (Remember, Node’s execution model is optimized for short tasks or async IO related tasks). A better approach is to use a tool that expertise in networking tasks – the most popular are nginx and HAproxy which are also used by the biggest cloud vendors to lighten the incoming load on node.js processes.
- Using nginx to compress server responses
- Your code must be identical across all environments, but amazingly npm lets dependencies drift across environments by default – when you install packages at various environments it tries to fetch packages’ latest patch version.
- Guard and restart your process upon failure
- `PM2`, `AWS ECS`, `Kubernetes`, `pm2-docker`
- In development, you started your app simply from the command line with node server.js or something similar. But doing this in production is a recipe for disaster. If the app crashes, it will be offline until you restart it. To ensure your app restarts if it crashes, use a process manager. A process manager is a “container” for applications that facilitate deployment, provides high availability, and enables you to manage the application at runtime.
- Understanding Node.js Clustering in Docker-Land “Docker containers are streamlined, lightweight virtual environments, designed to simplify processes to their bare minimum. Processes that manage and coordinate their own resources are no longer as valuable. Instead, management stacks like Kubernetes, Mesos, and Cattle have popularized the concept that these resources should be managed infrastructure-wide. CPU and memory resources are allocated by “schedulers”, and network resources are managed by stack-provided load balancers.
- Utilize all CPU cores: advanced use cases, consider replicating the NODE process using custom deployment script and balancing using a specialized tool such as nginx or use a container engine such as AWS ECS or Kubernetees that have advanced features for deployment and replication of processes.
- APM (application performance monitoring) refers to a family of products that aims to monitor application performance from end to end, also from the customer perspective.
* Make you code production ready:
  - The twelve-factor guide
  - Be stateless – Save no data locally on a specific web server
  - Cache – Utilize cache heavily, yet never fail because of cache mismatch
  - Test memory – gauge memory usage and leaks as part your development flow, tools such as `memwatch` can greatly facilitate this task
  - Name functions – Minimize the usage of anonymous functions (i.e. inline callback) as a typical memory profiler will provide memory usage per method name
  - Use CI tools – Use CI tool to detect failures before sending to production. For example, use ESLint to detect reference errors and undefined variables. Use –trace-sync-io to identify code that uses synchronous APIs (instead of the async version)
  - Log wisely – Include in each log statement contextual information, hopefully in JSON format so log aggregators tools such as Elastic can search upon those properties (see separate bullet – ‘Increase visibility using smart logs’). Also, include transaction-id that identifies each request and allows to correlate lines that describe the same transaction (see separate bullet – ‘Include Transaction-ID’)
  - Error management – Error handling is the Achilles’ heel of Node.js production sites – many Node processes are crashing because of minor errors while others hang on alive in a faulty state instead of crashing.
- memory issues are a known Node’s gotcha one must be aware of. Above all, memory usage must be monitored constantly. In the development and small production sites, you may gauge manually using Linux commands or npm tools and libraries like node-inspector and memwatch.
- The main drawback of this manual activities is that they require a human being actively monitoring – for serious production sites, it’s absolutely vital to use robust monitoring tools e.g. (AWS CloudWatch, DataDog or any similar proactive system) that alerts when a leak happens.
- There are also few development guidelines to prevent leaks: avoid storing data on the global level, use streams for data with dynamic size, limit variables scope using let and const.
- Get your frontend assets out of Node
  - Using a reverse proxy – your static files will be located right next to your Node application, only requests to the static files folder will be served by a proxy that sits in front of your Node app such as nginx. Using this approach, your Node app is responsible deploying the static files but not to serve them. Your frontend’s colleague will love this approach as it prevents cross-origin-requests from the frontend.
  - Cloud storage – your static files will NOT be part of your Node app content, they will be uploaded to services like AWS S3, Azure BlobStorage, or other similar services that were born for this mission. Using this approach, your Node app is not responsible deploying the static files neither to serve them, hence a complete decoupling is drawn between Node and the Frontend which is anyway handled by different teams.

##### Security
- Make use of security-related linter plugins such as eslint-plugin-security to catch security vulnerabilities and issues as early as possible
- Security plugins for ESLint and TSLint such as `eslint-plugin-security` and `tslint-config-security` offer code security checks based on a number of known vulnerabilities
- Limit concurrent requests using a middleware
- Rate limiting can be used for security purposes, for example to slow down brute‑force password‑guessing attacks. It can help protect against DDoS attacks by limiting the incoming request rate to a value typical for real users, and (with logging) identify the targeted URLs. More generally, it is used to protect upstream application servers from being overwhelmed by too many user requests at the same time.
- Extract secrets from config files or use packages to encrypt them
- For rare situations where secrets do need to be stored inside source control, using a package such as `cryptr` allows these to be stored in an encrypted form as opposed to in plain text.
- There are a variety of tools available which use git commit to audit commits and commit messages for accidental additions of secrets, such as git-secrets.
- Prevent query injection vulnerabilities with ORM/ODM libraries
- always validate any data you are going to store and use proper data-mapping libraries to handle the dangerous work for you.
- Use SSL/TLS to encrypt the client-server connection
- Attackers could perform man-in-the-middle attacks, spy on your users' behaviour and perform even more malicious actions when the connection is unencrypted
- Using services like the `Let'sEncrypt` certificate authority providing free ssl/tls certificates, you can easily obtain a certificate to secure your application.
- Node.js frameworks like Express (based on the core https module) support ssl/tls based servers easily, so the configuration can be done in a few lines of additional code.
- You can also configure ssl/tls on your reverse proxy pointing to your application for example using `nginx` or HAProxy.
- Comparing secret values and hashes securely
- When comparing secret values or hashes like HMAC digests, you should use the `crypto.timingSafeEqual(a, b)` function Node provides out of the box since Node.js v6.6.0.
- Require MFA/2FA for important services and accounts
- Rotate passwords and access keys frequently, including SSH keys
- Apply strong password policies, both for ops and in-application user management
- Do not ship or deploy your application with any default credentials, particularly for admin users or external services you depend on
- Use only standard authentication methods like OAuth, OpenID, etc.  - avoid basic authentication
- Auth rate limiting: Disallow more than X login attempts (including password recovery, etc.) in a period of Y
- On login failure, don't let the user know whether the username or password verification failed, just return a common auth error
- Consider using a centralized user management system to avoid managing multiple account per employee (e.g. GitHub, AWS, Jenkins, etc) and to benefit from a battle-tested user management system
- Respect the principle of least privilege  -  every component and DevOps person should only have access to the necessary information and resources
- Never work with the console/root (full-privilege) account except for account management
- Run all instances/containers on behalf of a role/service account
- Assign permissions to groups and not to users. This should make permission management easier and more transparent for most cases
- Access to production environment internals is done through the internal network only, use SSH or other ways, but never expose internal services
- Restrict internal network access  - explicitly set which resource can access other resources
- If using cookies, configure it to "secured" mode where it's being sent over SSL only
- If using cookies, configure it for "same site" only so only requests from same domain will get back the designated cookies
- If using cookies, prefer "http only" configuration that prevent browser side JavaScript code from accessing the cookies
- Protect each VPC with strict and restrictive access rules
- Prioritize threats using any standard security threat modeling like STRIDE or DREAD
- Protect against DDoS attacks using HTTP(S) and TCP load balancers
- Perform periodic penetration tests by specialized agencies
- Only accept SSL/TLS connections, enforce Strict-Transport-Security using headers
- Separate the network into segments (i.e. subnets) and ensure each node has the least necessary networking access permissions
- Group all services/instances that need no internet access and explictly disallow any outgoing connection (a.k.a private subnet)
- Store all secrets in a vault products like AWS KMS, HashiCorp Vault or Google Cloud KMS
- Lock down sensitive instance metadata using metadata
- Encrypt data in transit when it leaves a physical boundary
- Don't include secrets in log statements
- Avoid showing plain passwords in the frontend, take necessary measures in the backend and never store sensitive information in plaintext
- Scan docker images for known vulnerabilities (using Docker's and other vendors offer scanning services)
- Enable automatic instance (machine) patching and upgrades to avoid running old OS versions that lack security patches
- Provide the user with both 'id', 'access' and 'refresh' token so the access token is short-lived and renewed with the refresh token
- Log and audit each API call to cloud and management services (e.g who deleted the S3 bucket?) using services like AWS CloudTrail
- Run the security checker of your cloud provider (e.g. AWS security trust advisor)
- Alert on remarkable or suspicious auditing events like user login, new user creation, permission change, etc
- Alert on irregular amount of login failures (or equivelant actions like forgot password)
- Include the time and username that initiated the update in each DB record
- Instruct the browser to load resources from the same domain only, using the Content-Security-Policy http header
- [Adjust the HTTP response headers for enhanced security](https://github.com/i0natan/nodebestpractices/blob/master/sections/security/secureheaders.md)
- Avoid using the Node.js crypto library for handling passwords, use `Bcrypt`
- Math.random() should also never be used as part of any password or token generation due to it's predictability.
- Escape HTML, JS and CSS output
- Many npm libraries and HTML templating engines provide escaping capabilities (example: `escape-html`, `node-esapi`)
- Validate the incoming JSON schemas
- JSON-schema is an emerging standard that is supported by many npm libraries and tools (e.g. `jsonschema`, `Postman`), `joi` is also highly popular with sweet syntax. Typically JSON syntax can't cover all validation scenario and custom code or pre-baked validation frameworks like `validator.js` come in handy.
- To validate user input, one of the best libraries you can pick is joi. Joi is an object schema description language and validator for JavaScript objects.
- Support blacklisting JWTs
- Due to this, when using JWT authentication, an application should manage a blacklist of expired or revoked tokens to retain user's security in the case a token needs to be revoked.
- A brute force protection middleware such as express-brute should be used inside an express application to prevent brute force/dictionary attacks on sensitive routes such as /admin or /login based on request properties such as the user name, or other identifiers such as body parameters
- An in-memory store such as Redis or MongoDB should be used in production to enforce the shared limit across application clusters.
- Run Node.js as non-root user
- In practice, most Node.js apps don't need root access and don't run with such privileges. However, there are two common scenarios that might push to root usage:
- Limit payload size using a reverse-proxy or a middleware
- Parsing request bodies, for example JSON-encoded payloads, is a performance-heavy operation, especially with larger requests. When handling incoming requests in your web application, you should limit the size of their respective payloads. Incoming requests with unlimited body/payload sizes can lead to your application performing badly or crashing due to a denial-of-service outage or other unwanted side-effects
- Many popular middleware-solutions for parsing request bodies, such as the already-included `body-parser` package for express, expose options to limit the sizes of request payloads, making it easy for developers to implement this functionality.
- You can also integrate a request body size limit in your reverse-proxy/web server software if supported. Below are examples for limiting request sizes using `express` and/or `nginx`.
- `eval()`, `setTimeout()` and `setInterval()` are global functions. The security concern of using these functions is the possibility that untrusted user input might find its way into code execution leading to server compromise, as evaluating user code essentially allows an attacker to perform any actions that you can. It is suggested to refactor code to not rely on the usage of these functions where user input could be passed to the function and executed.
- Avoid RegEx when possible or defer the task to a dedicated library like `validator.js`, or `safe-regex` to check if the RegEx pattern is safe.
- Avoid requiring/importing another file with a path that was given as parameter due to the concern that it could have originated from user input. This rule can be extended for accessing files in general (i.e. fs.readFile()) or other sensitive resources with dynamic variables originating from user input.

```JavaScript
// insecure, as helperPath variable may have been modified by user input
const uploadHelpers = require(helperPath);

// secure
const uploadHelpers = require('./helpers/upload');
```
- some npm libraries, like sandbox and vm2 allow execution of isolated code in 1 single line of code. Though this latter option wins in simplicity it provides a limited protection

- avoid user input in every case, otherwise validate and sanitize it
- limit the privileges of the parent and child processes using user/group identities
- run your process inside of an isolated environment to prevent unwanted side-effects if the other preparations fail
- Never pass unsanitized user input to this function. Any input containing shell metacharacters may be used to trigger arbitrary command execution.
- Exposing application error details to the client in production should be avoided due to the risk of exposing sensitive application details such as server file paths, third-party modules in use, and other internal workflows of the application which could be exploited by an attacker.
- Any step in the development chain should be protected with MFA (multi-factor authentication), npm/Yarn are a sweet opportunity for attackers who can get their hands on some developer's password. Using developer credentials, attackers can inject malicious code into libraries that are widely installed across projects and services. Maybe even across the web if published in public. Enabling 2-factor-authentication in npm leaves almost zero chances for attackers to alter your package code.
- Each web framework and technology has its known weaknesses - telling an attacker which web framework we use is a great help for them. Using the default settings for session middlewares can expose your app to module- and framework-specific hijacking attacks in a similar way to the X-Powered-By header. Try hiding anything that identifies and reveals your tech stack (E.g. Node.js, express)
- Avoid DOS attacks by explicitly setting when a process should crash
- There's no instant remedy for this but a few techniques can mitigate the pain: Alert with critical severity anytime a process crashes due to an unhandled error, validate the input and avoid crashing the process due to invalid user input, wrap all routes with a catch and consider not to crash when an error originated within a request (as opposed to what happens globally)
- This is just an educated guess: given many Node.js applications, if we try passing an empty JSON body to all POST requests - a handful of applications will crash. At that point, we can just repeat sending the same request to take down the applications with ease
- Prevent unsafe redirects
- When redirects are implemented in Node.js and/or Express, it's important to perform input validation on the server-side. If an attacker discovers that you are not validating external, user-supplied input, they may exploit this vulnerability by posting specially-crafted links on forums, social media, and other public places to get users to click it.
- Fortunately the mitigation methods for this vulnerability are quite straightforward — don’t use unvalidated user input as the basis for redirect.
- The suggested fix to avoid unsafe redirects is to avoid relying on user input. If user input must be used, safe redirect whitelists can be used to avoid exposing the vulnerability.
