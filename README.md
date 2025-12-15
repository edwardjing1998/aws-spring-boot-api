npx stryker run

npx stryker init

npm install --save-dev @stryker-mutator/vitest-runner


 {
  "$schema": "./node_modules/@stryker-mutator/core/schema/stryker-schema.json",
  "testRunner": "vitest",
  "vitest": {
    "configFile": "vite.config.mjs"
  },
  "mutate": [
    "src/**/ClientReportIntegrationService.ts",
    "src/**/ClientReportAutoCompleteInputBox.tsx",
    "!src/**/*.test.{ts,tsx}"
  ],
  "reporters": [
    "progress",
    "clear-text",
    "html"
  ],
  "coverageAnalysis": "perTest"
}

