+ // Duplicate-mode input
+ const [targetSysPrin, setTargetSysPrin] = useState('');



+ {/* Duplicate controls */}
+ {mode === 'duplicate' && (
+   <Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr', gap: 2 }}>
+     <TextField
+       size="small"
+       label="Target SysPrin"
+       placeholder="Enter target sys/prin code (e.g., 12121212)"
+       value={targetSysPrin}
+       onChange={(e) => setTargetSysPrin(e.target.value)}
+       InputProps={{ sx: { fontSize: '0.85rem' } }}
+     />
+   </Box>
+ )}
