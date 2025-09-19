ERROR in src/Rapid/Onload/CommonComponent/AccountInfoDetails.tsx:1:20
TS2307: Cannot find module 'react-router/dist/development/routeModules-rOzWJJ9x' or its corresponding type declarations.
  > 1 | import { aC } from "react-router/dist/development/routeModules-rOzWJJ9x"
      |                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    2 | import { AppDispatch } from "../../../store"
    3 | import { getAccountTokenId, getAcctNumValue, getAs400ClientId, getAs400SystemId, getBasicSupplementalId, getCustId, getCustId2, getDisposition, getEntityInd, getFirstName, getHomePhone, getIssuanceDt, getIssuedByAmex, getLastName, getMailerId, getMailMethod, getMarketCode, getMsgId, getMsIssueDate, getPiIdTokenId, getPrimaryPiId, getPrimaryPiIdTokenId, getRoleCode, getSourceFile, getWorkPhone } from "../../Redux/Case"
    4 | import { accountDetails, RetrieveAddressDetailsType } from "../../types/AccountDetailstype"
