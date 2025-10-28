- const EditReMailOptions = ({ selectedData, setSelectedData, isEditable }) => {
+ const EditReMailOptions = ({ selectedData, setSelectedData, isEditable, onChangeGeneral }) => {
  const [updating, setUpdating] = useState(false);
+ // central helper (same pattern as General)
+ const pushGeneralPatch = (patch) => {
+   const withKeys = {
+     client: selectedData?.client,
+     sysPrin: selectedData?.sysPrin,
+     ...patch,
+   };
+   if (typeof onChangeGeneral === 'function') onChangeGeneral(withKeys);
+   else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKeys }));
+ };
