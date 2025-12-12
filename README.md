// src/views/sys-prin-configuration/EditClientReport.types.tsx

// Represents the data structure used for rows in the table
export interface ClientReportRow {
  reportName: string;
  reportId: number | null;
  receive: string;      // '0'|'1'|'2'
  destination: string;  // '0'|'1'|'2'
  fileText: string;     // '0'|'1'|'2'
  email: string;        // '0'|'1'|'2'
  password: string;
  emailBodyTx: string;
  fileExt: string;
}

// Represents the raw data coming from the API or parent props
export interface ClientReportOption {
  reportId?: number;
  receiveFlag?: boolean | number;
  outputTypeCd?: number;
  fileTypeCd?: number;
  emailFlag?: number;
  reportPasswordTx?: string;
  emailBodyTx?: string;
  fileExt?: string;
  reportDetails?: {
    queryName?: string;
    reportId?: number;
    fileExt?: string;
    [key: string]: any;
  };
  [key: string]: any;
}

export interface EditClientReportProps {
  selectedGroupRow: {
    client?: string;
    clientId?: string;
    billingSp?: string;
    reportOptionTotal?: number;
    reportOptions?: ClientReportOption[];
    [key: string]: any;
  } | null;
  isEditable: boolean;
  onDataChange?: (updatedGroupRow: any) => void;
}
