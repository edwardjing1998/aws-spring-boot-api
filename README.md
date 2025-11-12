+     // tell parent so it can refresh/remove the item
+     onClientDeleted?.(viewRow.client);
+     // optional: clear local selection or close window
+     setSelectedGroupRow?.(null);
+     onClose?.();
