const SysPrinInformationWindow = ({
    onClose,
    mode,
    selectedData,
    setSelectedData,
    selectedGroupRow,
+   setSelectedGroupRow,          // ⬅️ NEW (optional)
    onChangeVendorReceivedFrom,
    onChangeVendorSentTo,
    onPatchSysPrinsList,
  }) => {




// parent.jsx
const [selectedGroupRow, setSelectedGroupRow] = useState(null);
const [selectedData, setSelectedData] = useState({});

// ... your other logic

<SysPrinInformationWindow
  onClose={handleClose}
  mode={mode}
  selectedData={selectedData}
  setSelectedData={setSelectedData}
  selectedGroupRow={selectedGroupRow}
  setSelectedGroupRow={setSelectedGroupRow}      // ⬅️ pass it down
  onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
  onChangeVendorSentTo={onChangeVendorSentTo}
  onPatchSysPrinsList={onPatchSysPrinsList}
/>
