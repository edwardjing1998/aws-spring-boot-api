+     // inform parent to remove it from the list
+     onClientDeleted?.(viewRow.client);

+     // clear local selection and close
+     setSelectedGroupRow?.(null);
+     onClose?.();
