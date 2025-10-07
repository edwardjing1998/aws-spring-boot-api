 // top of the component state
 const [clientInformationWindow, setClientInformationWindow] = useState({ open: false, mode: 'edit' });
 const [sysPrinInformationWindow, setSysPrinInformationWindow] = useState({ open: false, mode: 'edit' });
+const [clientActionsDisabled, setClientActionsDisabled] = useState(true);






 // when a row is clicked
 const handleRowClick = (rowData) => {
   if (rowData.isGroup) {
     setSelectedGroupRow(rowData);
+    setClientActionsDisabled(true);       // disable when a group is selected
     return;
   }

   const billingSp = rowData.billingSp || '';
   const matchedClient = clientMap.get(billingSp);
   ...
   const mappedData = mapRowDataToSelectedData(
     selectedData, rowData, atmCashPrefixes, clientEmails, reportOptions, sysPrinsList
   );
   setSelectedData(mappedData);
+  setClientActionsDisabled(false);        // enable when a real client row is selected






 // the two buttons
 <Button
   variant="outlined"
   onClick={() => setClientInformationWindow({ open: true, mode: 'delete' })}
   size="small"
   sx={{ fontSize: '0.78rem', marginLeft: 'auto', marginRight: '6px', textTransform: 'none' }}
+  disabled={clientActionsDisabled}
 >
   Delete Client
 </Button>

 <Button
   variant="outlined"
   onClick={() => setClientInformationWindow({ open: true, mode: 'edit' })}
   size="small"
   sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
+  disabled={clientActionsDisabled}
 >
   Edit Client
 </Button>





