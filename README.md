export const FlattenClientData = (
  clients,
  selectedClient,
  expandedGroups,
  isWildcardMode,
  sysPrinPageByClient = {},   // ⭐ NEW: per-client page index map
  sysPrinPageSize             // ⭐ NEW: page size for sysPrins
) => {
  const flattenedData = [];

  // 💡 default page size if not provided
  const effectiveSysPrinPageSize =
    typeof sysPrinPageSize === 'number' && sysPrinPageSize > 0
      ? sysPrinPageSize
      : Number.MAX_SAFE_INTEGER;

  // 💡 Ensure clients is an array
  const clientsArray = Array.isArray(clients) ? clients : [];

  const clientsToShow =
    selectedClient === 'ALL'
      ? clientsArray
      : clientsArray.filter((c) => c.client === selectedClient);

  clientsToShow.forEach((clientGroup) => {
    const clientId = clientGroup.client;
    const isExpanded = expandedGroups[clientId] ?? false;

    // --- group row ---
    flattenedData.push({
      isGroup: true,
      groupLevel: 1,
      groupLabel: (
        <span style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
          <span
            style={{
              display: 'inline-block',
              width: '18px',
              height: '18px',
              border: '1px solid #aaa',
              textAlign: 'center',
              fontSize: '12px',
              lineHeight: '16px',
              borderRadius: '3px',
              userSelect: 'none',
            }}
          >
            {isExpanded ? '−' : '+'}
          </span>
          <span>{`${clientId} - ${clientGroup.name}`}</span>
        </span>
      ),
      client: clientId,
      ...clientGroup,
      memoType: 'Pending',
    });

    // --- child sysPrin rows (paged) ---
    if (isExpanded) {
      const allSysPrins = clientGroup.sysPrins || [];

      const pageIndex = sysPrinPageByClient[clientId] ?? 0;
      const start = pageIndex * effectiveSysPrinPageSize;
      const end = start + effectiveSysPrinPageSize;
      const pageSysPrins = allSysPrins.slice(start, end);

      pageSysPrins.forEach((sysPrin) => {
        flattenedData.push({
          isGroup: false,
          client: clientId,
          sysPrin: sysPrin.sysPrin,
          name: clientGroup.name,
          address: clientGroup.addr,
          city: clientGroup.city,
          state: clientGroup.state,
          zip: clientGroup.zip,
          contact: clientGroup.contact,
          phone: clientGroup.phone,
          faxNumber: clientGroup.faxNumber,
          billingSp: clientGroup.billingSp,
          excludeFromReport: clientGroup.excludeFromReport,
          positiveReports: clientGroup.positiveReports,
          subClientInd: clientGroup.subClientInd,
          subClientXref: clientGroup.subClientXref,
          amexIssued: clientGroup.amexIssued,
          reportBreakFlag: clientGroup.reportBreakFlag,
          chLookUpType: clientGroup.chLookUpType,
          active: clientGroup.active,
          sysPrinActive: sysPrin?.sysPrinActive,
        });
      });
    }
  });

  const clientGroupsOnly = flattenedData.filter((row) => row.isGroup);

  const pagedGroups = isWildcardMode
    ? clientGroupsOnly
    : clientGroupsOnly.slice(0); // still no client-level paging

  const visibleRows = [];

  pagedGroups.forEach((groupRow) => {
    visibleRows.push(groupRow);
    if (expandedGroups[groupRow.client]) {
      const children = flattenedData.filter(
        (row) => !row.isGroup && row.client === groupRow.client
      );
      visibleRows.push(...children);
    }
  });

  return visibleRows;
};
