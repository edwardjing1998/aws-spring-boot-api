import { Button, Typography } from '@mui/material';
import React, { useEffect, useMemo, useState } from 'react';
import { CCard, CCardBody } from '@coreui/react';

const PAGE_SIZE = 10;
const COLUMNS = 4;

/* =========================================================
   ✅ Inlined safe utils (from ../utils/safe)
   ========================================================= */

/** Return an array; if input is null/undefined/not-array -> [] */
function asArray<T = any>(v: any): T[] {
  return Array.isArray(v) ? (v as T[]) : [];
}

/** Convert to number; if not a finite number -> undefined */
function asNumber(v: any): number | undefined {
  if (v === null || v === undefined) return undefined;
  if (typeof v === 'number') return Number.isFinite(v) ? v : undefined;
  const n = Number(String(v).trim());
  return Number.isFinite(n) ? n : undefined;
}

/* =========================================================
   ✅ Inlined DTO types (from ../types/traceDtos)
   - Keep them minimal: only fields this component needs.
   ========================================================= */

export type VendorDto = {
  id?: string | number;
  vendId?: string | number;
  vend_id?: string | number;

  name?: string;
  vendNm?: string;
  vend_nm?: string;

  // allow extra vendor fields without TS errors
  [key: string]: any;
};

export type VendorLinkDto = {
  // link fields (outer)
  sysPrin?: string;
  sys_prin?: string;

  vendorId?: string | number;
  vendId?: string | number;
  vend_id?: string | number;
  id?: string | number;

  queForMail?: string | boolean;
  queueForMail?: string | boolean;
  que_for_mail?: string | boolean;
  queForMailCd?: string | boolean;
  queue_for_mail_cd?: string | boolean;
  queformail_cd?: string | boolean;

  vendName?: string;
  vendorName?: string;
  name?: string;
  vend_nm?: string;

  vendor?: VendorDto;

  // allow extra link fields without TS errors
  [key: string]: any;
};

export type SysPrinDto = {
  sysPrin?: string;
  sys_prin?: string;

  // the array we actually render
  vendorReceivedFrom?: VendorLinkDto[] | null;

  // optional totals (display only)
  vendorReceivedFromCount?: number | string | null;
  vendorForReceivedFromTotal?: number | string | null;

  // allow extra sysPrin fields without TS errors
  [key: string]: any;
};

/* =========================================================
   Component
   - Unified data contract: pass sysPrin object
   - Reads sysPrin.vendorReceivedFrom
   ========================================================= */

interface PreviewFilesReceivedFromProps {
  sysPrin?: SysPrinDto | null;
}

const PreviewFilesReceivedFrom: React.FC<PreviewFilesReceivedFromProps> = ({ sysPrin }) => {
  const [page, setPage] = useState<number>(0);

  const rows = useMemo(() => {
    const arr = asArray<VendorLinkDto>(sysPrin?.vendorReceivedFrom);

    return arr.map((it: VendorLinkDto) => {
      // id candidates
      const id =
        it.vendorId ??
        it.vendId ??
        it.vend_id ??
        it.id ??
        it.vendor?.vendId ??
        it.vendor?.vend_id ??
        it.vendor?.id ??
        '';

      // name candidates
      const name =
        it.vendName ??
        it.vendorName ??
        it.vendor?.vendNm ??
        it.vendor?.vend_nm ??
        it.vendor?.name ??
        it.name ??
        it.vend_nm ??
        '';

      const displayName = (name || '').toString().trim();
      const displayId = (id || '').toString().trim();

      const queueRaw =
        it.queueForMail ??
        it.queForMail ??
        it.que_for_mail ??
        it.queForMailCd ??
        it.queue_for_mail_cd ??
        it.queformail_cd;

      const queueForMail =
        typeof queueRaw === 'string'
          ? ['1', 'Y', 'TRUE'].includes(queueRaw.trim().toUpperCase())
          : !!queueRaw;

      return {
        ...it,
        __id: displayId,
        __displayName: displayName || displayId,
        __q: queueForMail,
      };
    });
  }, [sysPrin]);

  const pageCount = Math.ceil(rows.length / PAGE_SIZE) || 0;
  const pageData = rows.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);
  const hasData = rows.length > 0;

  // Optional totals (display only)
  const vendorReceivedFromCount = asNumber(sysPrin?.vendorReceivedFromCount);
  const vendorForReceivedFromTotal = asNumber(sysPrin?.vendorForReceivedFromTotal);

  alert("vendorReceivedFromCount " + vendorReceivedFromCount);

  alert("vendorForReceivedFromTotal " + vendorForReceivedFromTotal);

  useEffect(() => {
    if (page > 0 && page >= pageCount) setPage(0);
  }, [page, pageCount]);

  useEffect(() => {
    if (hasData) console.info('[PreviewFilesReceivedFrom] rows:', rows);
  }, [rows, hasData]);

  const cellStyle: React.CSSProperties = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 200,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
  };

  const headerStyle: React.CSSProperties = {
    ...cellStyle,
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
  };

  return (
    <>
      <CCard
        style={{
          height: '35px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          backgroundColor: '#f3f6f8',
          boxShadow: 'none',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent',
            justifyContent: 'space-between',
          }}
        >
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: 500 }}>General</p>

          {/* purely display; does not affect existing behaviors */}
          <div style={{ display: 'flex', gap: 10, alignItems: 'center' }}>
            {vendorReceivedFromCount !== undefined && (
              <span style={{ fontSize: '0.72rem', opacity: 0.85 }}>
                Received Count: {vendorReceivedFromCount}
              </span>
            )}
            {vendorForReceivedFromTotal !== undefined && (
              <span style={{ fontSize: '0.72rem', opacity: 0.85 }}>
                Available Total: {vendorForReceivedFromTotal}
              </span>
            )}
          </div>
        </CCardBody>
      </CCard>

      <CCard style={{ height: '320px', marginBottom: '4px', marginTop: '15px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
          <div style={{ width: '100%', height: '100%' }}>
            <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
              {/* Grid Table */}
              <div
                style={{
                  flex: 1,
                  display: 'grid',
                  gridTemplateColumns: '1fr',
                  rowGap: '0px',
                  columnGap: '4px',
                  minHeight: '250px',
                  alignContent: 'start',
                }}
              >
                {/* Header Row */}
                <div style={headerStyle}>Name</div>

                {/* Data Rows */}
                {pageData.length > 0 ? (
                  pageData.map((item: any, index: number) => (
                    <div
                      key={`${item.__id || index}-${index}`}
                      style={cellStyle}
                      title={item.__id || ''}
                    >
                      {item.__displayName || <em style={{ color: '#6b7280' }}>(no name)</em>}
                      {item.__q ? ' (Q)' : ''}
                    </div>
                  ))
                ) : (
                  <Typography
                    sx={{
                      gridColumn: `span ${COLUMNS}`,
                      fontSize: '0.75rem',
                      padding: '0 16px',
                      color: 'text.secondary',
                    }}
                  >
                    xxxx - xxxx
                  </Typography>
                )}
              </div>

              {/* Pagination */}
              <div
                style={{
                  marginTop: '16px',
                  display: 'flex',
                  justifyContent: 'space-between',
                  alignItems: 'center',
                }}
              >
                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                  onClick={() => setPage((p) => Math.max(p - 1, 0))}
                  disabled={!hasData || page === 0}
                >
                  ◀ Previous
                </Button>

                <Typography fontSize="0.75rem">
                  Page {hasData ? page + 1 : 0} of {hasData ? pageCount : 0}
                </Typography>

                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                  onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
                  disabled={!hasData || page === pageCount - 1}
                >
                  Next ▶
                </Button>
              </div>
            </div>
          </div>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewFilesReceivedFrom;
