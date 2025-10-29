// ClientInformationPage.jsx
import React, { useState } from 'react';
import NavigationPanel from './NavigationPanel';
import ClientInformationWindow from './ClientInformationWindow';

// Optional: however you choose the initial mode & selection
const ClientInformationPage = () => {
  const [clientList, setClientList] = useState([]);
  const [currentPage, setCurrentPage] = useState(0);
  const [isWildcardMode, setIsWildcardMode] = useState(false);

  // selection shown in the editor window
  const [selectedGroupRow, setSelectedGroupRow] = useState(null);
  const [mode, setMode] = useState('new'); // 'new' | 'edit' | 'delete' | 'view'

  // Upsert helper
  const upsertClient = (list, saved) => {
    if (!saved || !saved.client) return list;
    const idx = list.findIndex(c => c.client === saved.client);
    if (idx >= 0) {
      const copy = [...list];
      copy[idx] = { ...copy[idx], ...saved };
      return copy;
    }
    // Insert new at top; change to bottom if you prefer
    return [saved, ...list];
  };

  // If you have a wildcard page fetcher:
  const onFetchWildcardPage = async (_page) => {
    // implement if you use wildcard searching; else omit
  };
  // If you fetch extra details when a client group is clicked:
  const onFetchGroupDetails = async (_clientId) => {
    // implement if needed; else omit
  };

  return (
    <>
      <NavigationPanel
        clientList={clientList}
        setClientList={setClientList}
        currentPage={currentPage}
        setCurrentPage={setCurrentPage}
        isWildcardMode={isWildcardMode}
        setIsWildcardMode={setIsWildcardMode}
        onFetchWildcardPage={onFetchWildcardPage}
        onFetchGroupDetails={onFetchGroupDetails}
        onRowClick={(row) => {
          // decide mode & selection when a row is clicked
          setSelectedGroupRow(row?.isGroup ? row : row); // adapt for your flow
          setMode('edit'); // or 'view', depending on your UX
        }}
      />

      <ClientInformationWindow
        onClose={() => setSelectedGroupRow(null)}
        selectedGroupRow={selectedGroupRow}
        setSelectedGroupRow={setSelectedGroupRow}
        mode={mode}

        // ✅ NEW: update the list after create/update
        onClientCreated={(saved) => {
          setClientList(prev => upsertClient(prev, saved));
          setSelectedGroupRow(saved); // focus the new client
          setMode('edit');            // switch to edit after creation (optional)
        }}
        onClientUpdated={(saved) => {
          setClientList(prev => upsertClient(prev, saved));
          setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
        }}
      />
    </>
  );
};

export default ClientInformationPage;
