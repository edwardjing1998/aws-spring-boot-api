  <EditInvalidedAreaWindow
    ...
-   onCreated={(areaCode) => {
+   onCreated={(areaCode) => {
      const exists = selectedInvalidAreas.some(
        (n) => String(n).toLowerCase() === String(areaCode).toLowerCase()
      );
      if (exists) return;
      const newNames = [...selectedInvalidAreas, areaCode];
      setSelectedInvalidAreas(newNames);
-     updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
+     const nextArr = normaliseAreaArray(newNames);
+     updateField('invalidDelivAreas')(nextArr);     // local + parent
    }}
-   onDeleted={(areaCode) => {
+   onDeleted={(areaCode) => {
      const newNames = selectedInvalidAreas.filter(
        (n) => String(n).toLowerCase() !== String(areaCode).toLowerCase()
      );
      setSelectedInvalidAreas(newNames);
-     updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
+     const nextArr = normaliseAreaArray(newNames);
+     updateField('invalidDelivAreas')(nextArr);     // local + parent
    }}
  />
