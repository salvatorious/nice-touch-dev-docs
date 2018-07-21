Stylelint will fix most of the things it's mad about if you use this command
`yarn run stylelint --fix resources/assets/styles/_nice-touch-old.scss`  
  
Add rules with null to the config in `package.json` to turn them off
```
"stylelint": {
  "extends": "stylelint-config-standard",
  "rules": {
    "no-empty-source": null,
    "no-descending-specificity": null, // added this temporarily to compile
    "at-rule-no-unknown": [
      true,
      {
        "ignoreAtRules": [
          "extend",
          "at-root",
          "debug",
          "warn",
          "error",
          "if",
          "else",
          "for",
          "each",
          "while",
          "mixin",
          "include",
          "content",
          "return",
          "function"
        ]
      }
    ]
  }
},
```