import React from 'react';
import type { ClientRow, NavigationRow } from '../NavigationPanel';

export const FlattenClientData = (
  clients: ClientRow[],
  selectedClient: string,
  expandedGroups: Record<string, boolean>,
  isWildcardMode: boolean,
  sysPrinPageByClient: Record<string, number> = {},
  sysPrinPageSize?: number
): NavigationRow[] => {
  const flattenedData: NavigationRow[] = [];

  const effectiveSysPrinPageSize =
    typeof sysPrinPageSize === 'number' && sysPrinPageSize > 0
      ? sysPrinPageSize
      : Number.MAX_SAFE_INTEGER;

  const clientsArray = Array.isArray(clients) ? clients : [];

  const clientsToShow =
    selectedClient === 'ALL'
      ? clientsArray
      : clientsArray.filter((c) => c.client === selectedClient);

  clientsToShow.forEach((clientGroup) => {
    const clientId = clientGroup.client;
    const isExpanded = expandedGroups[clientId] ?? false;

    // group row
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
          <span>{`${clientId} - ${clientGroup.name ?? ''}`}</span>
        </span>
      ),
      ...clientGroup,
      memoType: 'Pending',
    });

    if (isExpanded) {
      const allSysPrins = clientGroup.sysPrins || [];

      const pageIndex = sysPrinPageByClient[clientId] ?? 0;
      const start = pageIndex * effectiveSysPrinPageSize;
      const end = start + effectiveSysPrinPageSize;
      const pageSysPrins = allSysPrins.slice(start, end);

      // pager row – one per expanded client per page
      flattenedData.push({
        isPagerRow: true,
        client: clientId,
      });

      pageSysPrins.forEach((sysPrin: any) => {
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

  const visibleRows: NavigationRow[] = [];

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
