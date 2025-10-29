// …imports stay the same…
import ClientInformationWindow from './utils/ClientInformationWindow';
import SysPrinInformationWindow from './utils/SysPrinInformationWindow';

const ClientInformationPage = () => {
  // …existing state & effects unchanged…

  // NEW: apply email list changes from the modal to both clientList and selectedGroupRow
  const handleClientEmailsChanged = React.useCallback((clientId, nextEmailList) => {
    if (!clientId) return;

    setClientList((prev) =>
      prev.map((c) =>
        c.client === clientId ? { ...c, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] } : c
      )
    );

    setSelectedGroupRow((prev) =>
      prev?.client === clientId ? { ...(prev ?? {}), clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] } : prev
    );
  }, []);

  // …rest of your helpers/handlers remain unchanged…

  return (
    <div className="d-flex flex-column" style={{ minHeight: '100vh', width: '80vw', overflow: 'visible' }}>
      {/* …top controls, grid, right panes unchanged… */}

      <Modal
        open={clientInformationWindow.open}
        onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
        aria-labelledby="client-info-modal"
        aria-describedby="client-info-modal-description"
      >
        <Box
          sx={{
            position: 'absolute',
            top: '50%',
            left: '50%',
            transform: 'translate(-50%, -50%)',
            width: '860px',
            height: '680px',
            bgcolor: 'background.paper',
            boxShadow: 24,
            borderRadius: 2,
            p: 2,
            overflow: 'visible',
            maxHeight: 'none',
          }}
        >
          <ClientInformationWindow
            onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            mode={clientInformationWindow.mode}
            // wire up (optional) existing create/update callbacks if you use them
            onClientCreated={(saved) => {
              if (!saved) return;
              setClientList((prev) => {
                const idx = prev.findIndex((c) => c.client === saved.client);
                if (idx >= 0) {
                  const copy = [...prev];
                  copy[idx] = { ...copy[idx], ...saved };
                  return copy;
                }
                return [saved, ...prev];
              });
              setSelectedGroupRow(saved);
              setClientInformationWindow({ open: true, mode: 'edit' });
            }}
            onClientUpdated={(saved) => {
              if (!saved) return;
              setClientList((prev) =>
                prev.map((c) => (c.client === saved.client ? { ...c, ...saved } : c))
              );
              setSelectedGroupRow((prev) => ({ ...(prev ?? {}), ...(saved ?? {}) }));
            }}
            // NEW: emails change bubbled up from child
            onClientEmailsChanged={handleClientEmailsChanged}
          />
        </Box>
      </Modal>

      {/* …SysPrin modal unchanged… */}
    </div>
  );
};

export default ClientInformationPage;
