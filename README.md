 import { useEffect, useState } from 'react';
 import AddIcon from '@mui/icons-material/Add';
 import { Box } from '@mui/material';
+import EditInvalidedAreaWindow from './utils/EditInvalidedAreaWindow'; // adjust path if needed

 // ... your other imports remain

 const EditReMailOptions = ({ selectedData, setSelectedData, isEditable }) => {
   const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;
   const compactCellSx = { py: 0.1, px: 1 };

   const normaliseAreaArray = (arr) =>
     arr.map((area) =>
       typeof area === 'string' ? { area, sysPrin: selectedData.sysPrin ?? '' } : area
     );

   const getAreaNames = () =>
     getvalue('invalidDelivAreas', []).map((a) => (typeof a === 'string' ? a : a.area));

   const [selectedInvalidAreas, setSelectedInvalidAreas] = useState([]);
+  const [openAreaWindow, setOpenAreaWindow] = useState(false);

   useEffect(() => {
     setSelectedInvalidAreas(getAreaNames());
   }, [selectedData?.invalidDelivAreas]);

   // Debug helper kept…

   const updateField = (field) => (value) =>
     setSelectedData((prev) => ({ ...prev, [field]: value }));

   // Kept: handleInvalidAreasChange (not used)

-  // Add a single area (prompt-based)
-  const handleAddArea = () => {
-    if (!isEditable) return;
-    const input = window.prompt('Enter area name to add:');
-    if (input == null) return;
-    const name = input.trim();
-    if (!name) return;
-    const exists = selectedInvalidAreas.some((n) => n.toLowerCase() === name.toLowerCase());
-    if (exists) {
-      alert('Area already exists.');
-      return;
-    }
-    const newNames = [...selectedInvalidAreas, name];
-    setSelectedInvalidAreas(newNames);
-    updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
-  };
+  // Open dialog to add a single area
+  const handleAddArea = () => {
+    if (!isEditable) return;
+    setOpenAreaWindow(true);
+  };

   const handleDeleteArea = (areaName) => {
     const newNames = selectedInvalidAreas.filter((n) => n !== areaName);
     setSelectedInvalidAreas(newNames);
     updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
   };

   const font78 = { fontSize: '0.73rem' };
   const leftLabel = { fontSize: '0.75rem', fontWeight: 500, minWidth: '160px', marginLeft: '2px' };
   const hasAreas = selectedInvalidAreas.length > 0;

   return (
     <CRow className="d-flex justify-content-between align-items-stretch">
       {/* LEFT column ...unchanged... */}

       {/* RIGHT column */}
       <CCol xs={6} className="d-flex justify-content-end">
         <CCard className="mb-0 w-100" style={{ height: 'auto' }}>
           <CCardBody className="d-flex flex-column gap-3">
             <TableContainer /* ...existing props... */>
               <Table size="small" stickyHeader aria-label="Do Not Deliver table">
                 <TableHead /* ...existing props... */>
                   <TableRow sx={{ '& th': compactCellSx }}>
                     <TableCell sx={{ ...compactCellSx, ...font78 }}>
                       <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
                     </TableCell>
                     <TableCell sx={{ ...compactCellSx }} align="right">
                       <IconButton
                         aria-label="Add area"
                         onClick={handleAddArea}
                         disabled={!isEditable}
                         size="small"
                         sx={{ width: 22, height: 22, p: 0, border: '1px solid #1976d2', bgcolor: '#fff', color: '#1976d2', borderRadius: '6px',
                               '&:hover': { bgcolor: '#e3f2fd' },
                               '&.Mui-disabled': { borderColor: 'divider', color: 'action.disabled', bgcolor: 'transparent' } }}
                       >
                         <AddIcon sx={{ fontSize: 14 }} />
                       </IconButton>
                     </TableCell>
                   </TableRow>
                 </TableHead>

                 <TableBody>
                   {hasAreas ? (
                     selectedInvalidAreas.map((name, idx) => (
                       <TableRow key={`${name}-${idx}`} sx={{ '& td': compactCellSx }}>
                         <TableCell sx={{ ...compactCellSx, ...font78 }}>{name}</TableCell>
                         <TableCell sx={{ ...compactCellSx }} align="right">
                           <IconButton
                             size="small"
                             aria-label={`Delete ${name}`}
                             onClick={() => handleDeleteArea(name)}
                             disabled={!isEditable}
                           >
                             <DeleteIcon fontSize="small" />
                           </IconButton>
                         </TableCell>
                       </TableRow>
                     ))
                   ) : null}
                 </TableBody>
               </Table>
               {!hasAreas && (
                 <Box /* ...empty-state overlay as you had... */>
                   <em style={{ fontSize: '0.73rem', color: 'rgba(0,0,0,0.6)' }}>
                     No areas selected.
                   </em>
                 </Box>
               )}
             </TableContainer>

             {/* Non-US / PO Box / Invalid State sections ... unchanged ... */}
           </CCardBody>
         </CCard>
       </CCol>

+      {/* --- Add Area Dialog --- */}
+      <EditInvalidedAreaWindow
+        open={openAreaWindow}
+        onClose={() => setOpenAreaWindow(false)}
+        sysPrin={selectedData?.sysPrin || ''}
+        onCreated={(areaCode) => {
+          // prevent duplicates (case-insensitive)
+          const exists = selectedInvalidAreas.some((n) => n.toLowerCase() === areaCode.toLowerCase());
+          if (exists) return;
+          const newNames = [...selectedInvalidAreas, areaCode];
+          setSelectedInvalidAreas(newNames);
+          updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
+        }}
+      />
     </CRow>
   );
 };

 export default EditReMailOptions;
