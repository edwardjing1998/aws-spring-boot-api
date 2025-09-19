// src/Rapid/Onload/CommonComponent/AccountInfoDetails.tsx

// ❌ removed: import { aC } from "react-router/dist/development/routeModules-..."
// (No public react-router-dom APIs are used in this file, so we don't import it.)

import { AppDispatch } from "../../../store";
import {
  getAccountTokenId,
  getAcctNumValue,
  getAs400ClientId,
  getAs400SystemId,
  getBasicSupplementalId,
  getCustId,
  getCustId2,
  getDisposition,
  getEntityInd,
  getFirstName,
  getHomePhone,
  getIssuanceDt,
  getIssuedByAmex,
  getLastName,
  getMailerId,
  getMailMethod,
  getMarketCode,
  getMsgId,
  getMsIssueDate,
  getPiIdTokenId,
  getPrimaryPiId,
  getPrimaryPiIdTokenId,
  getRoleCode,
  getSourceFile,
  getWorkPhone
} from "../../Redux/Case";
import { accountDetails, RetrieveAddressDetailsType } from "../../types/AccountDetailstype";
import { AddressDetailsType } from "../../types/AddressTypes";
import { AmexDetails, chType } from "../../types/CardHolderTypes";
import { SaveCaseType } from "../../types/NacType";
import { getPI_IDNumber } from "../../Redux/SaveTransactionData";

// ------------------------ helpers ------------------------

/** Safely split a full name into first/last. */
const splitName = (full?: string): { firstName: string; lastName: string } => {
  const trimmed = (full ?? "").trim();
  if (!trimmed) return { firstName: "", lastName: "" };
  const parts = trimmed.split(/\s+/);
  const firstName = parts[0] ?? "";
  const lastName = parts.length > 1 ? parts.slice(1).join(" ") : "";
  return { firstName, lastName };
};

// ------------------------ builders ------------------------

export const AccountInfo = (details: AmexDetails, cardType: string): accountDetails => {
  const { firstName, lastName } = splitName(details?.name);

  return {
    details: {
      caseNumber: "",
      piId: details?.saccount ?? "",
      customerId: "",
      primaryPiId: details?.saccount ?? "",
      account: details?.saccount ?? "",
      lastName,
      firstName,
      homePhone: "",
      workPhone: "",
      entityCode: "",
      roleCode: "",
      piStatus: "",
      status: "",
      active: 0,
      reason: "",
      subReason: null,
      disposition: "",
      inHour: "",
      inDate: "",
      nextDate: "",
      outDate: "",
      autoDate: "",
      numCards: null,
      finalActionCardsNr: null,
      deliveryId: null,
      sysPrin: "",
      cycle: "",
      firstUpdateVendId: "",
      contactCode: "",
      contactPhoneNumber: "",
      returnReasonCode: "",
      issuanceCode: "",
      issuanceDate: details?.issueDate ?? "",
      operatorCode: "",
      barcodeTypeCode: "",
      fileSent: "",
      postageBilled: "",
      issuedByAmex: 0,
      recordTypeText: "",
      serviceTypeText: "",
      mailerId: "",
      as400ClientId: details?.clientId ?? "",
      as400SystemId: details?.as400SystemId ?? "",
      basicSupplementalId: details?.bscSpplmntlId ?? "",
      originalMailDate: "",
      msgId: details?.msgId ?? "",
      mailMethod: details?.mlMthd ?? "",
      sourceFile: "",
      custId: "",
      msIssueDate: "",
      custId2: "",
      marketCode: details?.mktCd ?? "",
      accountTokenId: "",
      piIdTokenId: "",
      primaryPiIdTokenId: ""
    },

    result: details?.result ?? "",
    clientId: details?.clientId ?? "",
    systemId: details?.systemId ?? "",
    CardType: cardType
  };
};

export const AmexaddressDetails = (
  addressDetails: AddressDetailsType
): RetrieveAddressDetailsType => {
  return {
    primaryAddress: {
      addr1: addressDetails?.ad1 ?? "",
      addr2: addressDetails?.ad2 ?? "",
      addr3: addressDetails?.ad3 ?? "",
      addr4: addressDetails?.ad4 ?? "",
      city: "",
      state: "",
      zip: addressDetails?.zcode ?? ""
    },
    piIdAddress: {
      addr1: addressDetails?.ad1 ?? "",
      addr2: addressDetails?.ad2 ?? "",
      addr3: addressDetails?.ad3 ?? "",
      addr4: addressDetails?.ad4 ?? "",
      city: "",
      state: "",
      zip: addressDetails?.zcode ?? ""
    }
  };
};

export const ChAddressDetails = (
  addressDetails: AddressDetailsType
): RetrieveAddressDetailsType => {
  const base = {
    addr1: addressDetails?.ad1 ?? "",
    addr2: addressDetails?.ad2 ?? "",
    addr3: addressDetails?.ad3 ?? "",
    addr4: addressDetails?.ad4 ?? "",
    city: "",
    state: "",
    zip: addressDetails?.zcode ?? ""
  };

  if (addressDetails?.addressEntityCd === 1) {
    return { piIdAddress: base };
  } else if (addressDetails?.addressEntityCd === 2) {
    return { primaryAddress: base };
  } else {
    // ✅ fixed key spelling: originalAddress (was "oriignalAddress")
    return { originalAddress: base } as RetrieveAddressDetailsType;
  }
};

export const caseAccountInfo = (details: chType, cardType: string): accountDetails => {
  return {
    details: {
      caseNumber: details?.caseNumber ?? "",
      piId: details?.account ?? "",
      customerId: details?.customerId ?? "",
      primaryPiId: details?.account ?? "",
      account: details?.account ?? "",
      // ✅ fixed swap: use provided last/first correctly
      lastName: details?.lastName ?? "",
      firstName: details?.firstName ?? "",
      homePhone: details?.homePhone ?? "",
      workPhone: details?.workPhone ?? "",
      entityCode: details?.entityCode ?? "",
      roleCode: details?.roleCode ?? "",
      piStatus: details?.piStatus ?? "",
      status: details?.status ?? "",
      active: details?.active ?? 0,
      reason: details?.reason ?? "",
      subReason: details?.subReason ?? null,
      disposition: details?.disposition ?? "",
      inHour: details?.inHour ?? "",
      inDate: details?.inDate ?? "",
      nextDate: details?.nextDate ?? "",
      outDate: details?.outDate ?? "",
      autoDate: details?.autoDate ?? "",
      numCards: details?.numCards ?? null,
      finalActionCardsNr: details?.finalActionCardsNr ?? null,
      deliveryId: details?.deliveryId ?? null,
      sysPrin: details?.sysPrin ?? "",
      cycle: details?.cycle ?? "",
      firstUpdateVendId: details?.firstUpdateVendId ?? "",
      contactCode: details?.contactCode ?? "",
      contactPhoneNumber: details?.contactPhoneNumber ?? "",
      returnReasonCode: details?.returnReasonCode ?? "",
      issuanceCode: details?.issuanceCode ?? "",
      issuanceDate: details?.issuanceDate ?? "",
      operatorCode: details?.operatorCode ?? "",
      barcodeTypeCode: details?.barcodeTypeCode ?? "",
      fileSent: details?.fileSent ?? "",
      postageBilled: details?.postageBilled ?? "",
      issuedByAmex: details?.issuedByAmex ?? 0,
      recordTypeText: details?.recordTypeText ?? "",
      serviceTypeText: details?.serviceTypeText ?? "",
      mailerId: details?.mailerId ?? "",
      as400ClientId: details?.as400ClientId ?? "",
      as400SystemId: details?.as400SystemId ?? "",
      basicSupplementalId: details?.basicSupplementalId ?? "",
      originalMailDate: details?.originalMailDate ?? "",
      msgId: details?.msgId ?? "",
      mailMethod: details?.mailMethod ?? "",
      sourceFile: details?.sourceFile ?? "",
      custId: details?.custId ?? "",
      msIssueDate: details?.msIssueDate ?? "",
      custId2: details?.custId2 ?? "",
      marketCode: details?.marketCode ?? "",
      accountTokenId: details?.accountTokenId ?? "",
      piIdTokenId: details?.piIdTokenId ?? "",
      primaryPiIdTokenId: details?.primaryPiIdTokenId ?? ""
    },

    result: "",
    clientId: "",
    systemId: "",
    CardType: cardType
  };
};

// ------------------------ side effects (redux) ------------------------

export const SaveCaseDetails = (accountInfo: SaveCaseType, dispatch: AppDispatch) => {
  dispatch(getAs400SystemId(accountInfo?.as400SystemId));
  // dispatch(getCurrentTime(accountInfo?.cu));
  dispatch(getDisposition(accountInfo?.disposition));
  dispatch(getEntityInd(accountInfo?.entityCode));
  dispatch(getFirstName(accountInfo?.firstName));
  dispatch(getLastName(accountInfo?.lastName));
  dispatch(getAcctNumValue(accountInfo?.account));
  // dispatch(getCaseNumber(data21));
  // dispatch(getCustomerId(data22));
  dispatch(getPI_IDNumber(accountInfo?.account));
  dispatch(getPrimaryPiId(accountInfo?.primaryPiId));
  dispatch(getHomePhone(accountInfo?.homePhone));
  dispatch(getWorkPhone(accountInfo?.workPhone));
  dispatch(getRoleCode(accountInfo?.roleCode));
  dispatch(getIssuedByAmex(accountInfo?.issuedByAmex));
  dispatch(getMailerId(accountInfo?.mailerId));
  dispatch(getAs400ClientId(accountInfo?.as400ClientId));
  dispatch(getBasicSupplementalId(accountInfo?.basicSupplementalId));
  dispatch(getMsgId(accountInfo?.msgId));
  dispatch(getMailMethod(accountInfo?.mailMethod));
  dispatch(getSourceFile(accountInfo?.sourceFile));
  dispatch(getCustId(accountInfo?.custId));
  // dispatch(getMsIssueDate(accountInfo?.msIssueDate));
  dispatch(getCustId2(accountInfo?.custId2));
  dispatch(getMarketCode(accountInfo?.marketCode));
  dispatch(getAccountTokenId(accountInfo?.accountTokenId));
  dispatch(getPiIdTokenId(accountInfo?.piIdTokenId));

  // ✅ fixed: previously passed piIdTokenId twice. Use primaryPiIdTokenId here.
  dispatch(getPrimaryPiIdTokenId(accountInfo?.primaryPiIdTokenId));
};
