       <Box
         sx={{
           display: 'flex', justifyContent: 'space-between', alignItems: 'center',
           mt: '20px', backgroundColor: '#f3f6f8', height: '50px', width: '100%', px: 2, gap: 2,
         }}
       >
         <input
           type="text"
           placeholder="Enter Sys/Prin Name"
           value={selectedData?.sysPrin || ''}
           onChange={(e) => setSelectedData((prev) => ({ ...prev, sysPrin: e.target.value }))}
           disabled={!isEditable}
           style={{
             fontSize: '0.9rem', fontWeight: 400, width: '30vw', height: '30px',
             border: '1px solid #ccc', borderRadius: '4px', paddingLeft: '8px', backgroundColor: 'white',
           }}
         />
       </Box>

+      {/* Move controls */}
+      {mode === 'move' && (
+        <Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 2 }}>
+          <TextField
+            size="small"
+            label="Old Client ID"
+            value={oldClientId}
+            onChange={(e) => setOldClientId(e.target.value)}
+            InputProps={{ sx: { fontSize: '0.85rem' } }}
+          />
+          <TextField
+            size="small"
+            label="New Client ID"
+            value={newClientId}
+            onChange={(e) => setNewClientId(e.target.value)}
+            InputProps={{ sx: { fontSize: '0.85rem' } }}
+          />
+        </Box>
+      )}
