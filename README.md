// Build the patch we want to "copy to all" targets based on current editor values.
// It takes the same fields as buildCreatePayload, but strips identity and any
// slices you don't want to overwrite (customize the OMIT list as needed).
const getCopyPatchFromSelectedData = () => {
  // buildCreatePayload is a useMemo'd object with normalized values
  const base = buildCreatePayload || {};

  // Fields we NEVER want to copy over (identity + optional slices)
  const OMIT = new Set([
    'client',
    'sysPrin',
    'id',
    // If you do NOT want to overwrite these during Change All, keep them omitted:
    // 'vendorReceivedFrom',
    // 'vendorSentTo',
    // 'invalidDelivAreas',
    // 'notes',   // uncomment if notes should not be copied
  ]);

  // Create a shallow copy excluding the OMIT list
  const patch = Object.fromEntries(
    Object.entries(base).filter(([k]) => !OMIT.has(k))
  );

  // Optionally normalize types again (mostly no-ops if buildCreatePayload already did it)
  // Example: ensure numeric fields are numbers
  if (patch.tempAway != null)        patch.tempAway        = Number(patch.tempAway)        || 0;
  if (patch.tempAwayAtts != null)    patch.tempAwayAtts    = Number(patch.tempAwayAtts)    || 0;
  if (patch.reportMethod != null)    patch.reportMethod    = Number(patch.reportMethod)    || 0;
  if (patch.holdDays != null)        patch.holdDays        = Number(patch.holdDays)        || 0;

  // Example: ensure binary flags are strings '0'/'1' (if your backend expects strings)
  const ensure01 = (v) => (v === '1' || v === 1 || v === true) ? '1' : '0';
  [
    'rps','addrFlag','astatRch','nm13',
    'statA','statB','statC','statD','statE','statF','statI','statL','statO','statU','statX','statZ',
  ].forEach((k) => {
    if (k in patch) patch[k] = ensure01(patch[k]);
  });

  // Example: ensure Y/N fields are consistent
  const ensureYN = (v) => (v === 'Y' || v === true) ? 'Y' : (v === 'N' ? 'N' : 'N');
  ['nonUS', 'forwardingAddress'].forEach((k) => {
    if (k in patch) patch[k] = ensureYN(patch[k]);
  });

  // Example: make sure destroyStatus stays a single char your API accepts
  if ('destroyStatus' in patch && (patch.destroyStatus == null || patch.destroyStatus === '')) {
    patch.destroyStatus = '0'; // or ' ' if your API uses space for none
  }

  return patch;
};
