npx stryker run

npx stryker init

npm install --save-dev @stryker-mutator/vitest-runner


{
  "$schema": "./node_modules/@stryker-mutator/core/schema/stryker-schema.json",
  "testRunner": "vitest", 
  "vitest": {
    "configFile": "vitest.config.ts" 
  },
  "mutate": [
    "src/**/ClientReportIntegrationService.ts",
    "!src/**/*.test.ts"
  ],
  "reporters": [
    "progress",
    "clear-text",
    "html"
  ],
  "coverageAnalysis": "perTest"
}


