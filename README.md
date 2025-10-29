const upsertClient = useCallback((list, saved) => {
-  if (!saved || !saved.client) return list;
-  const idx = list.findIndex((c) => c.client === saved.client);
+  if (!saved) return list;
+  const idx = list.findIndex((c) =>
+    String(c?.client ?? '') === String(saved?.client ?? '') ||
+    (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''))
+  );
   if (idx >= 0) {
     const copy = [...list];
     copy[idx] = { ...copy[idx], ...saved };
     return copy;
   }
   return [saved, ...list];
}, []);
