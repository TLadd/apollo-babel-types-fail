# apollo-babel-types-fail
Example repo for error using apollo-cli to generate flow-types if you have a newer version of @babel/types installed

```bash
git clone git@github.com:TLadd/apollo-babel-types-fail.git
cd apollo-babel-types-fail
yarn
yarn genFlow
```

You will get the error
```
 ✔ Scanning for GraphQL queries (1 found)

 ✔ Loading GraphQL schema
 ✔ Parsing GraphQL schema
 ↓ Loading client-side GraphQL schema [skipped]
   → Path to client schema not provided
 ✖ Generating query files with 'flow' target
   → Property value expected type of string but got null
TypeError: Property value expected type of string but got null
    at Object.validate (~/github/apollo-babel-types-fail/node_modules/@babel/types/lib/definitions/utils.js:177:13)
    at validate (~/github/apollo-babel-types-fail/node_modules/@babel/types/lib/validators/validate.js:17:9)
    at builder (~/github/apollo-babel-types-fail/node_modules/@babel/types/lib/builders/builder.js:50:27)
    at Object.StringLiteralTypeAnnotation (~/github/apollo-babel-types-fail/node_modules/@babel/types/lib/builders/generated/index.js:1109:27)
    at graphql_1.sortEnumValues.map (~/github/apollo-babel-types-fail/node_modules/apollo-codegen-flow/lib/language.js:20:24)
    at Array.map (<anonymous>)
    at FlowAPIGenerator.enumerationDeclaration (~/github/apollo-babel-types-fail/node_modules/apollo-codegen-flow/lib/language.js:18:8)
    at FlowAPIGenerator.typeAliasForEnumType (~/github/apollo-babel-types-fail/node_modules/apollo-codegen-flow/lib/codeGeneration.js:89:35)
    at context.typesUsed.filter.forEach (~/github/apollo-babel-types-fail/node_modules/apollo-codegen-flow/lib/codeGeneration.js:27:19)
    at Array.forEach (<anonymous>)
error Command failed with exit code 1.
```

This error occurs because [this PR](https://github.com/babel/babel/pull/7706/files) made changes to some of the babel flow types to require a value being passed into the constructor.

apollo-codegen-core explicitly depends on `"@babel/types": "7.0.0-beta.38"`

apollo-codegen-flow depends on apollo-codegen-core, [does not depend @babel/types](https://github.com/apollographql/apollo-cli/blob/master/packages/apollo-codegen-flow/package.json#L35) explicitly, but still [imports from @babel/types](https://github.com/apollographql/apollo-cli/blob/master/packages/apollo-codegen-flow/src/language.ts#L18).

If your project or another dependency also depends on @babel/types, the version that is resolved when used in @babel/types might not be 7.0.0-beta.38, which is what causes the error.
