import { aC } from "react-router/dist/development/routeModules-rOzWJJ9x"
import { AppDispatch } from "../../../store"
import { getAccountTokenId, getAcctNumValue, getAs400ClientId, getAs400SystemId, getBasicSupplementalId, getCustId, getCustId2, getDisposition, getEntityInd, getFirstName, getHomePhone, getIssuanceDt, getIssuedByAmex, getLastName, getMailerId, getMailMethod, getMarketCode, getMsgId, getMsIssueDate, getPiIdTokenId, getPrimaryPiId, getPrimaryPiIdTokenId, getRoleCode, getSourceFile, getWorkPhone } from "../../Redux/Case"
import { accountDetails, RetrieveAddressDetailsType } from "../../types/AccountDetailstype"
import { AddressDetailsType, AddressTypes } from "../../types/AddressTypes"
import { AmexDetails, chType, ClientInfoType } from "../../types/CardHolderTypes"
import { SaveCaseType } from "../../types/NacType"
import { getPI_IDNumber } from "../../Redux/SaveTransactionData"
//,addrDetails:AddressTypes
export const AccountInfo=(details:AmexDetails,cardType:string):accountDetails=>{
    const fullName = details?.name;
const nameParts = fullName.split(" ");

const firstName = nameParts[0];
const lastName = nameParts.slice(1).join(" ");



    return{
    details:{
        caseNumber: "",
        piId: details?.saccount,
        customerId: "",
        primaryPiId: details?.saccount,
        account: details?.saccount,
        lastName: lastName,
        firstName: firstName,
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
        issuanceDate: details?.issueDate,
        operatorCode: "",
        barcodeTypeCode: "",
        fileSent: "",
        postageBilled: "",
        issuedByAmex: 0,
        recordTypeText: "",
        serviceTypeText: "",
        mailerId: "",
        as400ClientId: details?.clientId,
        as400SystemId: details?.as400SystemId,
        basicSupplementalId: details?.bscSpplmntlId,
        originalMailDate: "",
        msgId: details?.msgId,
        mailMethod: details?.mlMthd,
        sourceFile:"",
        custId: "",
        msIssueDate: "",
        custId2: "",
        marketCode: details?.mktCd,
        accountTokenId: "",
        piIdTokenId: "",
        primaryPiIdTokenId: ""
    },
    
    result:details?.result,
    clientId:details?.clientId,
    systemId:details?.systemId,
    CardType:cardType
}
}

export const AmexaddressDetails=(addressDetails:AddressDetailsType):RetrieveAddressDetailsType=>{
    return{
    primaryAddress: {
        addr1: addressDetails?.ad1,
  addr2: addressDetails?.ad2,
  addr3: addressDetails?.ad3,
  addr4: addressDetails?.ad4,
  city: "",
  state: "",
  zip: addressDetails?.zcode
    },
    piIdAddress: {
          addr1: addressDetails?.ad1,
  addr2: addressDetails?.ad2,
  addr3: addressDetails?.ad3,
  addr4: addressDetails?.ad4,
  city: "",
  state: "",
  zip: addressDetails?.zcode
    }
}

}

export const ChAddressDetails=(addressDetails:AddressDetailsType):RetrieveAddressDetailsType=>{
   
if(addressDetails?.addressEntityCd==1){
return{
piIdAddress: {
         addr1: addressDetails?.ad1,
  addr2: addressDetails?.ad2,
  addr3: addressDetails?.ad3,
  addr4: addressDetails?.ad4,
  city: "",
  state: "",
  zip: addressDetails?.zcode
    }
}
}else if(addressDetails?.addressEntityCd==2){
    return{
primaryAddress: {
         addr1: addressDetails?.ad1,
  addr2: addressDetails?.ad2,
  addr3: addressDetails?.ad3,
  addr4: addressDetails?.ad4,
  city: "",
  state: "",
  zip: addressDetails?.zcode
    }
}
}else{
    return{
oriignalAddress: {
         addr1: addressDetails?.ad1,
  addr2: addressDetails?.ad2,
  addr3: addressDetails?.ad3,
  addr4: addressDetails?.ad4,
  city: "",
  state: "",
  zip: addressDetails?.zcode
    }
}
}
    
}
export const caseAccountInfo=(details:chType,cardType:string):accountDetails=>{
    return{
    details:{
        caseNumber: details?.caseNumber,
        piId: details?.account,
        customerId: details?.customerId,
        primaryPiId: details?.account,
        account: details?.account,
        lastName: details?.firstName,
        firstName: details?.lastName,
        homePhone: details?.homePhone,
        workPhone: details?.workPhone,
        entityCode: details?.entityCode,
        roleCode: details?.roleCode,
        piStatus: details?.piStatus,
        status: details?.status,
        active: details?.active,
        reason: details?.reason,
        subReason: details?.subReason,
        disposition: details?.disposition,
        inHour: details?.inHour,
        inDate: details?.inDate,
        nextDate: details?.nextDate,
        outDate: details?.outDate,
        autoDate: details?.autoDate,
        numCards: details?.numCards,
        finalActionCardsNr: details?.finalActionCardsNr,
        deliveryId: details?.deliveryId,
        sysPrin: details?.sysPrin,
        cycle: details?.cycle,
        firstUpdateVendId: details?.firstUpdateVendId,
        contactCode: details?.contactCode,
        contactPhoneNumber: details?.contactPhoneNumber,
        returnReasonCode: details?.returnReasonCode,
        issuanceCode: details?.issuanceCode,
        issuanceDate: details?.issuanceDate,
        operatorCode: details?.operatorCode,
        barcodeTypeCode: details?.barcodeTypeCode,
        fileSent: details?.fileSent,
        postageBilled: details?.postageBilled,
        issuedByAmex: details?.issuedByAmex,
        recordTypeText: details?.recordTypeText,
        serviceTypeText: details?.serviceTypeText,
        mailerId: details?.mailerId,
        as400ClientId: details?.as400ClientId,
        as400SystemId: details?.as400SystemId,
        basicSupplementalId: details?.basicSupplementalId,
        originalMailDate:  details?.originalMailDate,
        msgId: details?.msgId,
        mailMethod: details?.mailMethod,
        sourceFile:details?.sourceFile,
        custId: details?.custId,
        msIssueDate: details?.msIssueDate,
        custId2: details?.custId2,
        marketCode: details?.marketCode,
        accountTokenId: details?.accountTokenId,
        piIdTokenId: details?.piIdTokenId,
        primaryPiIdTokenId: details?.primaryPiIdTokenId
    },
      
    result:"",
    clientId:"",
    systemId:"",
    CardType:cardType
}
}

export const SaveCaseDetails=(accountInfo:SaveCaseType,dispatch:AppDispatch)=>{
   
dispatch(getAs400SystemId(accountInfo?.as400SystemId));
//dispatch(getCurrentTime(accountInfo?.cu));
dispatch(getDisposition(accountInfo?.disposition));
dispatch(getEntityInd(accountInfo?.entityCode));
dispatch(getFirstName(accountInfo?.firstName));
dispatch(getLastName(accountInfo?.lastName));
//dispatch(getStatus(data8));
//dispatch(getSysPrinValue(data9));
//dispatch(getNumberOfCards(data10));
//dispatch(getIsBulkCard(data11));
//dispatch(getAddnlCards(data12));
//dispatch(getReason(data13));
//dispatch(getSubReason(data14));
//dispatch(getIssuanceCd(data15));
//dispatch(getIssuanceDt(accountInfo?.issuanceDate));
//dispatch(getActive(data17));
//dispatch(getNextDate(data18));
//dispatch(getOutDate(data19));
dispatch(getAcctNumValue(accountInfo?.account));
//dispatch(getCaseNumber(data21));
//dispatch(getCustomerId(data22));
dispatch(getPI_IDNumber(accountInfo?.account))
dispatch(getPrimaryPiId(accountInfo?.primaryPiId));
dispatch(getHomePhone(accountInfo?.homePhone));
dispatch(getWorkPhone(accountInfo?.workPhone));
dispatch(getRoleCode(accountInfo?.roleCode));
//dispatch(getPiStatus(data27));
// dispatch(getInHour(data28));
// dispatch(getInDate(data29));
// dispatch(getAutoDate(data30));
// dispatch(getFinalActionCardsNr(data31));
// dispatch(getDeliveryId(data32));
// dispatch(getCycle(data33));
// dispatch(getFirstUpdateVendId(data34));
// dispatch(getContactCode(data35));
// dispatch(getContactPhoneNumber(data36));
// dispatch(getReturnReasonCode(data37));
// dispatch(getOperatorCode(data38));
// dispatch(getBarcodeTypeCode(data39));
// dispatch(getFileSent(data40));
// dispatch(getPostageBilled(data41));
dispatch(getIssuedByAmex(accountInfo?.issuedByAmex));
// dispatch(getRecordTypeText(data43));
// dispatch(getServiceTypeText(data44));
dispatch(getMailerId(accountInfo?.mailerId));
dispatch(getAs400ClientId(accountInfo?.as400ClientId));
dispatch(getBasicSupplementalId(accountInfo?.basicSupplementalId));
// dispatch(getOriginalMailDate(data48));
dispatch(getMsgId(accountInfo?.msgId));
dispatch(getMailMethod(accountInfo?.mailMethod));
dispatch(getSourceFile(accountInfo?.sourceFile));
dispatch(getCustId(accountInfo?.custId));
//dispatch(getMsIssueDate(accountInfo?.msIssueDate));
dispatch(getCustId2(accountInfo?.custId2));
dispatch(getMarketCode(accountInfo?.marketCode));
dispatch(getAccountTokenId(accountInfo?.accountTokenId));
dispatch(getPiIdTokenId(accountInfo?.piIdTokenId));
dispatch(getPrimaryPiIdTokenId(accountInfo?.piIdTokenId))

}
