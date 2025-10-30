 import React, { useState, useEffect, useMemo } from 'react';
 import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
+import TextField from '@mui/material/TextField';
 import CloseIcon from '@mui/icons-material/Close';
 ...
   const [saving, setSaving] = useState(false);
+  // Move-mode inputs
+  const [oldClientId, setOldClientId] = useState(() => selectedGroupRow?.client ?? selectedData?.client ?? '');
+  const [newClientId, setNewClientId] = useState('');

   useEffect(() => {
     setIsEditable(mode === 'edit' || mode === 'new');
   }, [mode]);
