# nx-target-deps

Sample repository to show Nx missing local cache when it shouldn't.

# structure

There are two projects:

- one
- two

Via implicit dependencies in `nx.json`, project "two" depends on project "one".

Project "one" has a `build` target. This target has "forwardAllArgs" set to `false`.

Project "two" has a `deploy` target. This target has "forwardAllArgs" set to `true`.

Per `nx.json`, `build` is a cacheable operation.

## instructions

1. `npm i`
1. `nx run one:build`
1. `nx run two:deploy`

Observe a cache hit on `one:build`.

1. `nx run two:deploy --someArg=true`

Observe a cache miss on `one:build`.

1. `nx run two:deploy --someArg=false`

Observe a cache miss on `one:build`.

1. `nx run two:deploy --someArg=true`

Observe a cache hit on `one:build`.

1. `nx run two:deploy --someArg=false`

Observe a cache hit on `one:build`.

## expected

Because the `build` target in project "one" doesn't forward arguments, there should be a cache hit regardless of the arguments passed to the `deploy` target.

## actual

There is a cache miss if arguments differ from the original run.

# summary

Missing the cache when different arguments are encountered generally makes sense. But in this case, since arguments _are not forwarded_, the arguments are effectively the same as those of the results in the cache. Therefore, there should be a cache hit.
