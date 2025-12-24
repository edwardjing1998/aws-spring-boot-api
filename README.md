import { ClientRowData } from '../models/ClientRowData'

export const buildClientPayload = (row: ClientRowData = {}) => {
  const {
    client,
    name,
    addr,
    city,
    state,
    zip,
    contact,
    phone,
    active,
    faxNumber,
    billingSp,
    reportBreakFlag,
    chLookUpType,
    excludeFromReport,
    positiveReports,
    subClientInd,
    subClientXref,
    amexIssued,
  } = row

  return {
    client,
    name,
    addr,
    city,
    state,
    zip,
    contact,
    phone,
    active: !!active,
    faxNumber,
    billingSp,
    reportBreakFlag:
      typeof reportBreakFlag === 'string'
        ? Number(reportBreakFlag)
        : reportBreakFlag ?? 0,
    chLookUpType:
      typeof chLookUpType === 'string'
        ? Number(chLookUpType)
        : chLookUpType ?? 0,
    excludeFromReport: !!excludeFromReport,
    positiveReports: !!positiveReports,
    subClientInd: !!subClientInd,
    subClientXref,
    amexIssued: !!amexIssued,
  }
}
