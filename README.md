@use "@coreui/coreui/scss/coreui" as * with ( 
  $enable-deprecation-messages: false,
);



    "@coreui/icons": "^3.0.1",
    "@coreui/icons-react": "^2.3.0",
    "@coreui/react": "^5.9.1",




<w> null

ERROR in ./src/Client/scss/style.scss (./node_modules/css-loader/dist/cjs.js??ruleSet[1].rules[1].oneOf[7].use[1]!./node_modules/postcss-loader/dist/cjs.js??ruleSet[1].rules[1].oneOf[7].use[2]!./node_modules/resolve-url-loader/index.js??ruleSet[1].rules[1].oneOf[7].use[3]!./node_modules/sass-loader/dist/cjs.js??ruleSet[1].rules[1].oneOf[7].use[4]!./src/Client/scss/style.scss)
Module build failed (from ./node_modules/sass-loader/dist/cjs.js):
SassError: Undefined mixin.
   ╷
59 │ ┌ @include color-mode(dark) {
60 │ │   body {
61 │ │     background-color: var(--cui-dark-bg-subtle);
62 │ │   }
63 │ │ 
64 │ │   .footer {
65 │ │     --cui-footer-bg: var(--cui-body-bg);
66 │ │   }
67 │ └ }
   ╵
  src\Client\scss\style.scss 59:1  root stylesheet

webpack compiled with 1 error
Files successfully emitted, waiting for typecheck results...
