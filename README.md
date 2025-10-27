// ParentOfWindow.jsx (the component that owns sysPrinsList and selectedData)
const [sysPrinsList, setSysPrinsList] = useState([]);

const onPatchSysPrinsList = (sysPrinCode, patch, clientOpt) => {
  setSysPrinsList((list) =>
    list.map((sp) => {
      const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
      const client = sp?.id?.client  ?? sp?.client;
      const match  = clientOpt != null ? (code === sysPrinCode && client === clientOpt) : (code === sysPrinCode);
      return match ? { ...sp, ...patch } : sp;
    })
  );

  // Keep selectedData in sync too (quality-of-life)
  setSelectedData((prev) => (prev ? { ...prev, ...patch } : prev));
};

// Pass it down to the window
<SysPrinInformationWindow
  mode={mode}
  selectedData={selectedData}
  setSelectedData={setSelectedData}
  selectedGroupRow={selectedGroupRow}
  onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
  onChangeVendorSentTo={onChangeVendorSentTo}
  onPatchSysPrinsList={onPatchSysPrinsList}   // <-- NEW
/>
