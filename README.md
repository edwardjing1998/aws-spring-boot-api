   const handlePrimaryClick = async () => {
-    if (mode === 'new') {
+    if (mode === 'new') {
       await handleSaveCreate();
+    } else if (mode === 'duplicate') {
+      await handleDuplicate();
     } else if (mode === 'move') {
       await handleMove();
     } else {
       alert('Not in "new" mode. No create action executed.');
     }
   };
