+ // ✅ Whenever tableData changes for any reason (new/edit/delete or initial load),
+ //    normalize and push to parent so parent state always mirrors the table.
+ useEffect(() => {
+   if (!onDataChange) return;
+   const next = {
+     ...(selectedGroupRow ?? {}),
+     reportOptions: tableData.map(rowToOption),
+   };
+   onDataChange(next);
+   // eslint-disable-next-line react-hooks/exhaustive-deps
+ }, [tableData]); // intentionally depend only on tableData
