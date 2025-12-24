export interface ClientRowData {
  client?: string
  name?: string
  addr?: string
  city?: string
  state?: string
  zip?: string
  contact?: string
  phone?: string
  active?: boolean
  faxNumber?: string
  billingSp?: string
  reportBreakFlag?: string | number
  chLookUpType?: string | number
  excludeFromReport?: boolean
  positiveReports?: boolean
  subClientInd?: boolean
  subClientXref?: string
  amexIssued?: boolean
  [key: string]: any
}
