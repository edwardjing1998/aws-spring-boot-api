import React from 'react';

export interface SysPrinSummary {
  sysPrin: string;
  sysPrinActive?: string | boolean | null;
  // add more fields here if needed later
}

export interface ClientGroup {
  client: string;
  name: string;
  addr?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  faxNumber?: string;
  billingSp?: string;
  excludeFromReport?: string | boolean | null;
  positiveReports?: string | boolean | null;
  subClientInd?: string | null;
  subClientXref?: string | null;
  amexIssued?: string | boolean | null;
  reportBreakFlag?: string | null;
  chLookUpType?: string | null;
  active?: string | boolean | null;
  sysPrins?: SysPrinSummary[];
  // allow extra properties coming from backend
  [key: string]: any;
}

export interface GroupRow extends ClientGroup {
  isGroup: true;
  groupLevel: number;
  groupLabel: React.ReactNode;
  memoType: string;
}

export interface SysPrinRow {
  isGroup: false;
  client: string;
  sysPrin: string;
  name: string;
  address?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  faxNumber?: string;
  billingSp?: string;
  excludeFromReport?: string | boolean | null;
  positiveReports?: string | boolean | null;
  subClientInd?: string | null;
  subClientXref?: string | null;
  amexIssued?: string | boolean | null;
  reportBreakFlag?: string | null;
  chLookUpType?: string | null;
  active?: string | boolean | null;
  sysPrinActive?: string | boolean | null;
}

export type NavigationRow = GroupRow | SysPrinRow;

/**
 * Flattens client + sysPrin tree into a flat list of rows
 * with optional per-client sysPrin paging.
 */
export const FlattenClientData = (
  clients: ClientGroup[] | null | undefined,
  selectedClient: string,
  expandedGroups: Record<string, boolean>,
  isWildcardMode: boolean,
  sysPrinPageByClient: Record<string, number> = {}, // ⭐ per-client page index
  sysPrinPageSize?: number                          // ⭐ page size for sysPrins
): NavigationRow[] => {
  const flattenedData: NavigationRow[] = [];

  // 💡 default page size if not provided
  const effectiveSysPrinPageSize =
    typeof sysPrinPageSize === 'number' && sysPrinPageSize > 0
      ? sysPrinPageSize
      : Number.MAX_SAFE_INTEGER;

  // 💡 Ensure clients is an array
  const clientsArray: ClientGroup[] = Array.isArray(clients) ? clients : [];

  const clientsToShow: ClientGroup[] =
    selectedClient === 'ALL'
      ? clientsArray
      : clientsArray.filter((c) => c.client === selectedClient);

  clientsToShow.forEach((clientGroup) => {
    const clientId = clientGroup.client;
    const isExpanded = expandedGroups[clientId] ?? false;

    // --- group row ---
    const groupRow: GroupRow = {
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
      memoType: 'Pending',
      ...clientGroup,
    };

    flattenedData.push(groupRow);

    // --- child sysPrin rows (paged) ---
    if (isExpanded) {
      const allSysPrins: SysPrinSummary[] = clientGroup.sysPrins || [];

      const pageIndex = sysPrinPageByClient[clientId] ?? 0;
      const start = pageIndex * effectiveSysPrinPageSize;
      const end = start + effectiveSysPrinPageSize;
      const pageSysPrins = allSysPrins.slice(start, end);

      pageSysPrins.forEach((sysPrin) => {
        const row: SysPrinRow = {
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
        };

        flattenedData.push(row);
      });
    }
  });

  const clientGroupsOnly: NavigationRow[] = flattenedData.filter(
    (row) => row.isGroup
  );

  const pagedGroups: NavigationRow[] = isWildcardMode
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
