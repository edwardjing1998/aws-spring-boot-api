npx stryker run

npx stryker init

npm install --save-dev @stryker-mutator/vitest-runner


 10:33:49 (17220) INFO ConcurrencyTokenProvider Creating 11 test runner process(es).
10:34:42 (17220) INFO DryRunExecutor Starting initial test run (vitest test runner with "perTest" coverage analysis). This may take a while.
10:37:28 (17220) ERROR DryRunExecutor One or more tests resulted in an error:
        Test runner crashed. Tried twice to restart it without any luck. Last time the error message was: Error: Error: Failed to parse source for import analysis because the content contains invalid JS syntax. If you are using JSX, make sure to name the file with the .jsx or .tsx extension.
Error: Failed to parse source for import analysis because the content contains invalid JS syntax. If you are using JSX, make sure to name the file with the .jsx or .tsx extension.
    at TransformPluginContext._formatLog (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47897:41)
    at TransformPluginContext.error (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47894:16)
    at TransformPluginContext.transform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:45978:14)
    at async EnvironmentPluginContainer.transform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47692:18)
    at async loadAndTransform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:41327:27)
Error: Error: Failed to parse source for import analysis because the content contains invalid JS syntax. If you are using JSX, make sure to name the file with the .jsx or .tsx extension.
Error: Failed to parse source for import analysis because the content contains invalid JS syntax. If you are using JSX, make sure to name the file with the .jsx or .tsx extension.
    at TransformPluginContext._formatLog (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47897:41)
    at TransformPluginContext.error (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47894:16)
    at TransformPluginContext.transform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:45978:14)
    at async EnvironmentPluginContainer.transform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:47692:18)
    at async loadAndTransform (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/vite/dist/node/chunks/dep-Bid9ssRr.js:41327:27)
    at ChildProcess.<anonymous> (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:123:33)
    at ChildProcess.emit (node:events:507:28)
    at emit (node:internal/child_process:949:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21)
10:37:28 (17220) ERROR Stryker Unexpected error occurred while running Stryker Error: Something went wrong in the initial test run
    at DryRunExecutor.validateResultCompleted (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/process/3-dry-run-executor.js:76:15)
    at DryRunExecutor.executeDryRun (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/process/3-dry-run-executor.js:96:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
    at async WorkItem.execute (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/concurrent/pool.js:32:28)
    at async file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/concurrent/pool.js:69:13
10:37:28 (17220) INFO Stryker This might be a known problem with a solution documented in our troubleshooting guide.
10:37:28 (17220) INFO Stryker You can find it at https://stryker-mutator.io/docs/stryker-js/troubleshooting/
10:37:28 (17220) INFO Stryker Still having trouble figuring out what went wrong? Try `npx stryker run --fileLogLevel trace --logLevel debug` to get some more info.
node:internal/process/promises:394
    triggerUncaughtException(err, true /* fromPromise */);
    ^

Error: Something went wrong in the initial test run
    at DryRunExecutor.validateResultCompleted (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/process/3-dry-run-executor.js:76:15)
    at DryRunExecutor.executeDryRun (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/process/3-dry-run-executor.js:96:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
    at async WorkItem.execute (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/concurrent/pool.js:32:28)
    at async file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/concurrent/pool.js:69:13

Node.js v23.9.0
}

Node.js v23.9.0
PS C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin> 

