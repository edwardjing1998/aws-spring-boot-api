// ClientEmail.type.tsx

export interface ClientEmail {
  emailNameTx?: string;
  emailAddressTx?: string;
  mailServerId: number;
  reportId?: string | number;
  id?: { reportId?: string | number };
  activeFlag?: boolean | number;
  carbonCopyFlag?: boolean | number;
  [key: string]: any; // Allow other properties if backend sends them
}

export interface ClientGroupRow {
  client?: string;
  clientEmail?: ClientEmail[];
  clientEmailTotal?: number;
  [key: string]: any;
}

export interface ServiceResult {
  nextList: ClientEmail[];
  options: string[];
  clientId: string;
  statusMessage: string;
}

export interface EditClientEmailSetupProps {
  selectedGroupRow: ClientGroupRow | null;
  isEditable: boolean;
  onEmailsChanged?: (clientId: string, nextList: ClientEmail[]) => void;
}
