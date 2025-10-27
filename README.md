- const pushGeneralPatch = (patch) => {
-   const withKey = { sysPrin: selectedData?.sysPrin, ...patch };
+ const pushGeneralPatch = (patch) => {
+   const withKey = {
+     client: selectedData?.client,     // add this
+     sysPrin: selectedData?.sysPrin,   // keep this
+     ...patch
+   };
   console.log('[General] patch ->', withKey);
   if (typeof onChangeGeneral === 'function') onChangeGeneral(withKey);
   else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKey }));
 };
