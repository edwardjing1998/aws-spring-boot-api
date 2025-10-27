- const SysPrinInformationWindow = ({
+ const SysPrinInformationWindow = ({
   onClose,
   mode,
   selectedData,
   setSelectedData,
   selectedGroupRow,
   // existing focused updaters
   onChangeVendorReceivedFrom,
   onChangeVendorSentTo,
+  onPatchSysPrinsList, // NEW: bubble to the real owner of sysPrinsList
 }) => {
