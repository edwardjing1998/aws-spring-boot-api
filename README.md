/**
 * Maps the raw data from the grid row and related lists into the full selectedData object.
 * Preserves locally edited slices when re-clicking the same sysPrin.
 */
export function mapRowDataToSelectedData(
  prev,
  rowData,
  atmCashPrefixes,
  clientEmails,
  reportOptions,
  sysPrinsList
) {
  const matchedSysPrin = sysPrinsList.find(
    sp => sp?.id?.sysPrin === rowData.sysPrin || sp?.sysPrin === rowData.sysPrin
  );

  const sameSysPrin = (prev?.sysPrin || '') === (rowData?.sysPrin || '');

  // Prefer locally edited slices if we're still on the same sysPrin
  const vendorReceivedFrom =
    sameSysPrin && Array.isArray(prev?.vendorReceivedFrom)
      ? prev.vendorReceivedFrom
      : matchedSysPrin?.vendorReceivedFrom || [];

  const vendorSentTo =
    sameSysPrin && Array.isArray(prev?.vendorSentTo)
      ? prev.vendorSentTo
      : matchedSysPrin?.vendorSentTo || [];

  // Keep prev when same sysPrin; otherwise use rowData ?? matched ?? ''
  const keep = (key) =>
    sameSysPrin && prev?.[key] !== undefined
      ? prev[key]
      : (rowData?.[key] ?? matchedSysPrin?.[key] ?? '');

  // Also preserve the entire sysPrins list when staying on the same sysPrin
  const mergedSysPrins =
    sameSysPrin && Array.isArray(prev?.sysPrins) ? prev.sysPrins : sysPrinsList;

  return {
    ...prev,
    ...rowData,

    sysPrinsPrefixes: atmCashPrefixes,
    clientEmail: clientEmails,
    reportOptions: reportOptions,

    // keep same key if we’re on the same one
    sysPrin: sameSysPrin ? (prev?.sysPrin ?? rowData.sysPrin ?? '') : (rowData.sysPrin || ''),

    // critical: don't lose locally patched list row
    sysPrins: mergedSysPrins,

    invalidDelivAreas: matchedSysPrin?.invalidDelivAreas || [],

    // keep your local edits when re-clicking the same sysPrin
    vendorReceivedFrom,
    vendorSentTo,

    // notes + ALL general fields
    notes: keep('notes'),

    // status letters now also use keep(...)
    statA: keep('statA'),
    statB: keep('statB'),
    statC: keep('statC'),
    statD: keep('statD'),
    statE: keep('statE'),
    statF: keep('statF'),
    statI: keep('statI'),
    statL: keep('statL'),
    statO: keep('statO'),
    statU: keep('statU'),
    statX: keep('statX'),
    statZ: keep('statZ'),

    // general dropdowns/flags (preserve prev on same sysPrin)
    special:           keep('special'),
    pinMailer:         keep('pinMailer'),
    destroyStatus:     keep('destroyStatus'),
    custType:          keep('custType'),
    returnStatus:      keep('returnStatus'),
    addrFlag:          keep('addrFlag'),
    astatRch:          keep('astatRch'),
    sysPrinActive:            keep('sysPrinActive'),
    nm13:              keep('nm13'),
    rps:               keep('rps'),
    tempAway:          keep('tempAway'),
    holdDays:          keep('holdDays'),
    tempAwayAtts:      keep('tempAwayAtts'),
    undeliverable:     keep('undeliverable'),
    forwardingAddress: keep('forwardingAddress'),
    nonUS:             keep('nonUS'),
    poBox:             keep('poBox'),
    badState:          keep('badState'),
    sysPrinContact:    keep('sysPrinContact'),
    sysPrinPhone:      keep('sysPrinPhone'),
  };
}

export const defaultSelectedData = {
  client: '',
  name: '',
  address: '',
  billingSp: '',
  atmCashRule: '',
  active: '',
  notes: '',
  special: '',
  pinMailer: '',
  destroyStatus: '',
  contact: '',
  phone: '',
  custType: '',
  returnStatus: '',
  addrFlag: '',
  astatRch: '',
  sysPrinActive: '',
  nm13: '',
  rps: '',
  sysPrinsPrefixes: [],
  clientEmail: [],
  reportOptions: [],
  sysPrins: [],
  sysPrin: '',
  invalidDelivAreas: [],
  vendorReceivedFrom: [],
  vendorSentTo: [],
  statA: '',
  statB: '',
  statC: '',
  statE: '',
  statF: '',
  statI: '',
  statL: '',
  statU: '',
  statD: '',
  statO: '',
  statX: '',
  statZ: '',
  sysPrinContact: '',
  sysPrinPhone: ''
};
