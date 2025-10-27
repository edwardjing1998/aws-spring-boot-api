/**
 * Maps the raw data from the grid row and related lists into the full selectedData object.
 * Preserves locally edited slices when re-clicking the same sysPrin.
 */
export function mapRowDataToSelectedData(prev, rowData, atmCashPrefixes, clientEmails, reportOptions, sysPrinsList) {
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

  // helper to keep prev on same sysPrin, otherwise use rowData ?? matched ?? ''
  const keep = (key) =>
    sameSysPrin && prev?.[key] !== undefined
      ? prev[key]
      : (rowData?.[key] ?? matchedSysPrin?.[key] ?? '');

  return {
    ...prev,
    ...rowData,
    sysPrinsPrefixes: atmCashPrefixes,
    clientEmail: clientEmails,
    reportOptions: reportOptions,
    sysPrins: sysPrinsList,
    sysPrin: rowData.sysPrin || '',
    invalidDelivAreas: matchedSysPrin?.invalidDelivAreas || [],

    // keep your local edits when re-clicking the same sysPrin
    vendorReceivedFrom,
    vendorSentTo,

    // notes
    notes: keep('notes'),

    // status letters (use ?? so empty strings are preserved)
    statA: rowData?.statA ?? matchedSysPrin?.statA ?? '',
    statB: rowData?.statB ?? matchedSysPrin?.statB ?? '',
    statC: rowData?.statC ?? matchedSysPrin?.statC ?? '',
    statE: rowData?.statE ?? matchedSysPrin?.statE ?? '',
    statF: rowData?.statF ?? matchedSysPrin?.statF ?? '',
    statI: rowData?.statI ?? matchedSysPrin?.statI ?? '',
    statL: rowData?.statL ?? matchedSysPrin?.statL ?? '',
    statU: rowData?.statU ?? matchedSysPrin?.statU ?? '',
    statD: rowData?.statD ?? matchedSysPrin?.statD ?? '',
    statO: rowData?.statO ?? matchedSysPrin?.statO ?? '',
    statX: rowData?.statX ?? matchedSysPrin?.statX ?? '',
    statZ: rowData?.statZ ?? matchedSysPrin?.statZ ?? '',

    // general dropdowns/flags (preserve prev on same sysPrin)
    special:           keep('special'),
    pinMailer:         keep('pinMailer'),
    destroyStatus:     keep('destroyStatus'),
    custType:          keep('custType'),
    returnStatus:      keep('returnStatus'),
    addrFlag:          keep('addrFlag'),
    astatRch:          keep('astatRch'),
    active:            keep('active'),
    nm13:              keep('nm13'),
    tempAway:          keep('tempAway'),
    holdDays:          keep('holdDays'),
    tempAwayAtts:      keep('tempAwayAtts'),
    undeliverable:     keep('undeliverable'),
    forwardingAddress: keep('forwardingAddress'),
    nonUS:             keep('nonUS'),
    poBox:             keep('poBox'),
    badState:          keep('badState'),
    contact:           keep('contact'),
    phone:             keep('phone'),
  };
}

export const defaultSelectedData = {
  client: '',
  name: '',
  address: '',
  billingSp: '',
  atmCashRule: '',
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
  active: '',
  nm13: '',
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
};
