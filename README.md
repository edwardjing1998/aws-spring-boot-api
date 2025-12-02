interface NavigationPanelProps {
  onRowClick?: (row: NavigationRow) => void;
  clientList: ClientRow[];
  setClientList: React.Dispatch<React.SetStateAction<ClientRow[]>>;
  currentPage: number;
  setCurrentPage: React.Dispatch<React.SetStateAction<number>>;
  isWildcardMode: boolean;
  setIsWildcardMode: React.Dispatch<React.SetStateAction<boolean>>;
  onFetchWildcardPage?: (page: number) => void;
  onFetchGroupDetails?: (clientId: string) => void;
  // ⬇️ NEW
  onClearSelectedData?: () => void;
}
