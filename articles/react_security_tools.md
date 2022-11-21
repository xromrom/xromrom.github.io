# Open source tools for scanning ReactJS code

Below are my favourite open source tools for finding security issues at React source code.

## 1. Semgrep

[Semgrep](https://github.com/returntocorp/semgrep) is a fast, open-source, static analysis tool for finding bugs and enforcing code standards at editor, commit, and CI time.

To run React rules set against your code use the command:
```shell
semgrep --config=p/react ./app-folder
```

## 2. eslint-plugin-security
[This plugin](https://www.npmjs.com/package/eslint-plugin-security) will help identify potential security hotspots, but finds a lot of false positives which need triage by a human.

To setup do the following:
```shell
