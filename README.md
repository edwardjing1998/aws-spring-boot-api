// …imports unchanged…

const ClientInformationWindow = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
  onClientCreated,
  onClientUpdated,   // already present from previous step
  onClientEmailsChanged,
}) => {
  // …state/effects/helpers unchanged…

  // NEW: bridge child "client updated" to window + page
  const handleClientInfoUpdated = (saved) => {
    if (!saved) return;
    // keep local in-sync
    setSelectedGroupRow((prev) => ({ ...(prev ?? {}), ...(saved ?? {}) }));
    // bubble to page (updates list + preview)
    onClientUpdated?.(saved);
  };

  // …render header, tabs, etc.

  {/* Tab 1 content (Client Information) */}
  {tabIndex === 0 && (
    <>
      <CRow className="mb-2" style={borderPanelStyle}>
        <CCol>
          <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
            <EditClientInformation
              selectedGroupRow={viewRow}
              isEditable={isEditable}
              setSelectedGroupRow={setSelectedGroupRow}
              mode={mode}
              // NEW: pass the updater
              onClientUpdated={handleClientInfoUpdated}
            />
          </div>
        </CCol>
      </CRow>
      {renderButtonRow()}
    </>
  )}

  // …rest unchanged (email setup, reports, atm/cash) …
};

export default ClientInformationWindow;
