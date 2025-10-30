// inside ClientInformationWindow render (tabIndex === 0)
<EditClientInformation
  selectedGroupRow={viewRow}
  isEditable={isEditable}
  setSelectedGroupRow={setSelectedGroupRow}
  mode={mode}
+ onClientUpdated={onClientUpdated}
/>





+ const EditClientInformation = ({
+   selectedGroupRow,
+   isEditable,
+   setSelectedGroupRow,
+   mode,              // from parent
+   onClientUpdated,   // ✅ new: bubble up to parent
+ }) => {
