// inside ClientInformationWindow render (tabIndex === 0)
<EditClientInformation
  selectedGroupRow={viewRow}
  isEditable={isEditable}
  setSelectedGroupRow={setSelectedGroupRow}
  mode={mode}
+ onClientUpdated={onClientUpdated}
/>
