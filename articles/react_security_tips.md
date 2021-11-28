# ReactJS security tips

This article contains a few tips about making your ReactJS apps more secure.
It may be useful for AppSec Analysts performing security assessments or for React Developers trying to make their app more protected.

## 1. Prevent input-based attacks (XSS)
ReactJS is considered to be a relatively secure framework with regards to XSS protection, as the ReactDOM method used to render application contents features built-in escaping. By default, ReactDOM escapes any values embedded in JSX before rendering them, which helps to prevent XSS attacks.

However, the following dangerous functions should be avoided:

### dangerouslySetInnerHTML()
If it has to be used, implement thorough validation of the input and escaping of all user-supplied values.

### JSON.stringify()
It converts a JavaScript object or value to a JSON string, optionally replacing the values if a replacer function is specified or including only the specified properties if a replacer array is specified. Using this method can introduce an XSS vulnerability if the output is rendered without proper HTML escaping. Instead, please use the serialize-javascript library (https://github.com/yahoo/serialize-javascript)

### eval()
The function executes JavaScript code represented as a string. If it has to be used, implement thorough validation and contextual encoding of the user input passed to the function.

## 2. Check dependencies on vulnerabilities
The open-source components that your app uses may have known vulnerabilities, to check them you can use the command:
```shell
npm audit
```
or:
```shell
yarn audit
```

## 3. Scan source code with an open-source tool called semgrep

After [installing semgrep on your local machine](https://semgrep.dev/getting-started), use a command

```shell
semgrep --config=p/react ./app-folder
```

## 4. Check configuration files, look for secrets, API keys

Check the following files (if your project has such):
* .env* files
* .gitlab-ci.yml

## 5. Do not use localStorage for storing a secrets

Web Storage (localStorage/sessionStorage) is accessible through JavaScript on the same domain. This means that any JavaScript running on your site will have access to web storage, and because of this can be vulnerable to cross-site scripting (XSS) attacks.

Use cookies instead because of the additional security they provide, and the simplicity of protecting against CSRF with modern web frameworks.

Some discussion on that topic is [here](https://stackoverflow.com/questions/44133536/is-it-safe-to-store-a-jwt-in-localstorage-with-reactjs).

## 6. Look in the code for a personal or sensitive information leakage

Check code on excessive logging:
```js
console.log('Some sensitive info', object)
```

If your uses Sentry, check on
```js
Sentry.captureException(/*some sensitive info*/)
```

That's all, thanks for your attention!
