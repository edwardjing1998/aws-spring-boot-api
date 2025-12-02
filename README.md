const NavigationPanel: React.FC<NavigationPanelProps> = ({
  onRowClick,
  clientList,
  setClientList,
  currentPage,
  setCurrentPage,
  isWildcardMode,
  setIsWildcardMode,
  onFetchWildcardPage,
  onFetchGroupDetails,
  onClearSelectedData,   // ⬅️ new
}) => {
  ...
