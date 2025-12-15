npx stryker run

npx stryker init

npm install --save-dev @stryker-mutator/vitest-runner


 npx stryker run
10:27:55 (34432) INFO ProjectReader Found 1 of 488 file(s) to be mutated.
Browserslist: browsers data (caniuse-lite) is 8 months old. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
10:27:56 (34432) INFO Instrumenter Instrumented 1 source file(s) with 89 mutant(s)
10:28:03 (34432) INFO ConcurrencyTokenProvider Creating 11 test runner process(es).
10:28:36 (34432) ERROR Stryker Unexpected error occurred while running Stryker StrykerError: Error: Build failed with 1 error:
error: Could not resolve "C:\\Users\\F2LIPBX\\react\\fiserv-github\\react-rapid-admin\\.stryker-tmp\\sandbox-zV5C1E\\vitest.config.ts"
Error: Build failed with 1 error:
error: Could not resolve "C:\\Users\\F2LIPBX\\react\\fiserv-github\\react-rapid-admin\\.stryker-tmp\\sandbox-zV5C1E\\vitest.config.ts"
    at failureErrorWithLog (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:1477:15)
    at C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:946:25       
    at runOnEndCallbacks (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:1317:45)
    at buildResponseToResult (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:944:7)
    at C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:971:16       
    at responseCallbacks.<computed> (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:623:9)
    at handleIncomingPacket (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:678:12)
    at Socket.readFromStdout (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:601:7)
    at Socket.emit (node:events:507:28)
    at addChunk (node:internal/streams/readable:559:12)
    at ChildProcess.<anonymous> (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:123:33)
    at ChildProcess.emit (node:events:507:28)
    at emit (node:internal/child_process:949:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}
10:28:36 (34432) INFO Stryker This might be a known problem with a solution documented in our troubleshooting guide.
10:28:36 (34432) INFO Stryker You can find it at https://stryker-mutator.io/docs/stryker-js/troubleshooting/
10:28:36 (34432) INFO Stryker Still having trouble figuring out what went wrong? Try `npx stryker run --fileLogLevel trace --logLevel debug` to get some more info.
node:internal/process/promises:394
    triggerUncaughtException(err, true /* fromPromise */);
    ^

StrykerError: Error: Build failed with 1 error:
error: Could not resolve "C:\\Users\\F2LIPBX\\react\\fiserv-github\\react-rapid-admin\\.stryker-tmp\\sandbox-zV5C1E\\vitest.config.ts"
Error: Build failed with 1 error:
error: Could not resolve "C:\\Users\\F2LIPBX\\react\\fiserv-github\\react-rapid-admin\\.stryker-tmp\\sandbox-zV5C1E\\vitest.config.ts"
    at failureErrorWithLog (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:1477:15)
    at C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:946:25       
    at runOnEndCallbacks (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:1317:45)
    at buildResponseToResult (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:944:7)
    at C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:971:16       
    at responseCallbacks.<computed> (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:623:9)
    at handleIncomingPacket (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:678:12)
    at Socket.readFromStdout (C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\node_modules\esbuild\lib\main.js:601:7)
    at Socket.emit (node:events:507:28)
    at addChunk (node:internal/streams/readable:559:12)
    at ChildProcess.<anonymous> (file:///C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:123:33)
    at ChildProcess.emit (node:events:507:28)
    at emit (node:internal/child_process:949:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}

Node.js v23.9.0
PS C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin> 

