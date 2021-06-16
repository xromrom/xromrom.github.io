# ReactJS security tips

This article contains a few tips about making your ReactJS apps more secure.
It may be useful for AppSec Analysts performing security assessments or for React Developers trying to make their app more protected.

## 1. Check dependencies on vulnerabilities
The open-source components that your app uses may have known vulnerabilities, to check them you can use the command:
```shell
npm audit
```

## 2. Scan source code with an open-source tool called semgrep

After [installing semgrep on your local machine](https://semgrep.dev/getting-started), use a command

```shell
semgrep --config=p/react ./app-folder
```

## 3. Check configuration files, look for secrets, API keys

Check the following files (if your project has such):
* .env* files
* .gitlab-ci.yml

## 4. Do not use localStorage for storing a secrets

Web Storage (localStorage/sessionStorage) is accessible through JavaScript on the same domain. This means that any JavaScript running on your site will have access to web storage, and because of this can be vulnerable to cross-site scripting (XSS) attacks.

Use cookies instead because of the additional security they provide, and the simplicity of protecting against CSRF with modern web frameworks.

Some discussion on that topic is [here](https://stackoverflow.com/questions/44133536/is-it-safe-to-store-a-jwt-in-localstorage-with-reactjs).

## 5. Look in the code for a personal or sensitive information leakage

Check code on excessive logging:
```js
console.log('Some sensitive info', object)
```

If your uses Sentry, check on
```js
Sentry.captureException(/*some sensitive info*/)
```

That's all, thanks for your attention!
