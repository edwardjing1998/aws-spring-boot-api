<NavigationPanel
  onRowClick={handleRowClick}
  clientList={clientList}
  setClientList={setClientList}
  currentPage={currentPage}
  setCurrentPage={setCurrentPage}
  isWildcardMode={isWildcardMode}
  setIsWildcardMode={setIsWildcardMode}
  onFetchWildcardPage={fetchWildcardPage}
  // ⬇️ NEW: clear selectedData when a group is expanded
  onClearSelectedData={() => setSelectedData(defaultSelectedData)}
/>
