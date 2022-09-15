A mistyped parenthesis or unnecessary arithmetic was called in Token.sol at line 118:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118

The line is the following:

```
(baseTokenId += schedule) % 100;
```

The developer might have meant `baseTokenId += (schedule % 100);` in which case this is a low finding.

In case `baseTokenId += schedule;` is enough, this report can be considered as a gas optimization report.