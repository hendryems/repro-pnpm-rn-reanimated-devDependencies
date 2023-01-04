# Introduction

This is a bare minimum repo that serves as an example for [react-native-reanimated #3770](https://github.com/software-mansion/react-native-reanimated/issues/3770).

Note that the project itself does not build. The focus is on the generated `pnpm-lock.yaml` file where the resolution of the `react-native-reanimated` dependency can end up different due to different versions of `@babel/core` being specified:

```
  RNTSSample:
    specifiers:
    ...
    dependencies:
      '@babel/core': 7.15.0
      ...
      react-native-reanimated: 2.10.0_hnrjq27qonsllyitmvsx5byqwu
    ...


  packages/my-component:
    specifiers:
    ...
    dependencies:
      '@babel/core': 7.12.9
      react-native-reanimated: 2.10.0_@babel+core@7.12.9
    ...
```

This is problematic because global state requires by `react-native-reanimated` gets overwritten by the other copies if multiple version of the library is included.

The above `pnpm-lock.yaml` behavior seems to be stem from:

  1. `@babel/core` being specified as peer dependency specification in `react-native-reanimated` (and hence `packages/my-component` specified it as a dependency) and
  2. How [pnpm resolves peer dependencies](https://pnpm.io/how-peers-are-resolved).

`@babel/core` seems to only be required, by `RNTSSample` (the ReactNative app), as dev tooling. It would be nice, although unclear if its feasible, if there are other mechanism available to specify this instead of using peerDependencies.