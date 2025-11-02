// ✅ NEW: active must be "0"/"1" for parent/grid
  if ('active' in patch) patch.active = ensure01(patch.active);  // <-- NEW: active
