// src/views/sys-prin-configuration/EditAtmCashPrefix.types.tsx

export interface AtmCashPrefixRow {
  billingSp: string;
  prefix: string;
  atmCashRule: string; // '0', '1', or sometimes '' from inputs
  [key: string]: any;
}

export interface EditAtmCashPrefixProps {
  selectedGroupRow: {
    client?: string;
    clientId?: string;
    billingSp?: string;
    clientPrefixTotal?: number;
    sysPrinsPrefixes?: AtmCashPrefixRow[];
    [key: string]: any;
  } | null;
  onDataChange?: (updatedGroupRow: any) => void;
}
