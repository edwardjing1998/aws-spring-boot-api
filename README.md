import { Button, Typography } from "@mui/material";
import React, { useEffect, useMemo, useRef, useState } from "react";
import { CCard, CCardBody } from "@coreui/react";

// ✅ reuse your existing service
import { fetchVendorsReceivedFrom } from "./EditFileReceivedFrom.service";

const PAGE_SIZE = 10;
const COLUMNS = 4;

/* =========================================================
   ✅ Inlined safe utils
   ========================================================= */
function asArray<T = any>(v: any): T[] {
  return Array.isArray(v) ? (v as T[]) : [];
}

function asNumber(v: any): number | undefined {
  if (v === null || v === undefined) return undefined;
  if (typeof v === "number") return Number.isFinite(v) ? v : undefined;
  const n = Number(String(v).trim());
  return Number.isFinite(n) ? n : undefined;
}

/* =========================================================
   Types (minimal)
   ========================================================= */
export type VendorDto = {
  id?: string | number;
  vendId?: string | number;
  vend_id?: string | number;

  name?: string;
  vendNm?: string;
  vend_nm?: string;

  [key: string]: any;
};

export type VendorLinkDto = {
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

  [key: string]: any;
};

export type SysPrinDto = {
  sysPrin?: string;
  sys_prin?: string;

  // (optional initial page data coming from parent)
  vendorReceivedFrom?: VendorLinkDto[] | null;

  // totals (display + enable paging)
  vendorForSentToTotal?: number | string | null;
  vendorSentToTotal?: number | string | null;
  vendorForReceivedFromTotal?: number | string | null;
  vendorReceivedFromTotal?: number | string | null;

  [key: string]: any;
};

interface PreviewFilesReceivedFromProps {
  sysPrin?: SysPrinDto | null;
}

const PreviewFilesReceivedFrom: React.FC<PreviewFilesReceivedFromProps> = ({ sysPrin }) => {
  // ✅ server-side page
  const [page, setPage] = useState<number>(0);
  const [loading, setLoading] = useState(false);

  // ✅ rows displayed in this component (server page)
  const [serverRows, setServerRows] = useState<VendorLinkDto[]>([]);

  const sysPrinId = String(sysPrin?.sysPrin ?? sysPrin?.sys_prin ?? "");

  // totals
  const vendorReceivedFromTotal = asNumber(sysPrin?.vendorReceivedFromTotal);
  const vendorForReceivedFromTotal = asNumber(sysPrin?.vendorForReceivedFromTotal);
  const vendorForSentToTotal = asNumber(sysPrin?.vendorForSentToTotal);
  const vendorSentToTotal = asNumber(sysPrin?.vendorSentToTotal);

  // ✅ decide paging based on total
  const pageCount = useMemo(() => {
    const total = vendorReceivedFromTotal ?? 0;
    return Math.max(1, Math.ceil(total / PAGE_SIZE));
  }, [vendorReceivedFromTotal]);

  const hasPaging = (vendorReceivedFromTotal ?? 0) > PAGE_SIZE;

  // ✅ avoid stale/overlapping requests (important for fast clicking)
  const reqSeqRef = useRef(0);

  const loadPage = async (targetPage: number) => {
    if (!sysPrinId) {
      setServerRows([]);
      return;
    }
    const seq = ++reqSeqRef.current;
    setLoading(true);
    try {
      const raw = await fetchVendorsReceivedFrom(targetPage, PAGE_SIZE, sysPrinId);
      // only accept latest response
      if (seq !== reqSeqRef.current) return;
      setServerRows(asArray<VendorLinkDto>(raw));
    } catch (e) {
      if (seq !== reqSeqRef.current) return;
      console.error("[PreviewFilesReceivedFrom] fetch page failed:", e);
      setServerRows([]);
    } finally {
      if (seq === reqSeqRef.current) setLoading(false);
    }
  };

  // ✅ When sysPrin changes, reset page and load page 0
  useEffect(() => {
    setPage(0);

    if (!sysPrinId) {
      setServerRows([]);
      return;
    }

    // Prefer seeded data ONLY for first render of page 0 (optional)
    const seeded = asArray<VendorLinkDto>(sysPrin?.vendorReceivedFrom);
    if (seeded.length > 0) {
      setServerRows(seeded);

      // Optional: still refresh from server to ensure latest (uncomment if you want)
      // loadPage(0);
    } else {
      loadPage(0);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [sysPrinId]);

  // ✅ When page changes, load that page from server
  useEffect(() => {
    if (!sysPrinId) return;
    // If page==0 and we have seeded rows, you can skip refetch.
    // But your requirement says: clicking prev/next should refetch; page 0 initial can be seeded.
    if (page === 0) return; // page 0 already loaded in sysPrin change effect (seeded or fetch)
    loadPage(page);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [page]);

  // Normalize display rows
  const rows = useMemo(() => {
    return asArray<VendorLinkDto>(serverRows).map((it) => {
      const id =
        it.vendorId ??
        it.vendId ??
        it.vend_id ??
        it.id ??
        it.vendor?.vendId ??
        it.vendor?.vend_id ??
        it.vendor?.id ??
        "";

      const name =
        it.vendName ??
        it.vendorName ??
        it.vendor?.vendNm ??
        it.vendor?.vend_nm ??
        it.vendor?.name ??
        it.name ??
        it.vend_nm ??
        "";

      const displayName = (name || "").toString().trim();
      const displayId = (id || "").toString().trim();

      const queueRaw =
        it.queueForMail ??
        it.queForMail ??
        it.que_for_mail ??
        it.queForMailCd ??
        it.queue_for_mail_cd ??
        it.queformail_cd;

      const queueForMail =
        typeof queueRaw === "string"
          ? ["1", "Y", "TRUE"].includes(queueRaw.trim().toUpperCase())
          : !!queueRaw;

      return {
        ...it,
        __id: displayId,
        __displayName: displayName || displayId,
        __q: queueForMail,
      };
    });
  }, [serverRows]);

  const hasData = rows.length > 0;

  const cellStyle: React.CSSProperties = {
    backgroundColor: "white",
    minHeight: "25px",
    display: "flex",
    alignItems: "center",
    justifyContent: "flex-start",
    fontSize: "0.78rem",
    fontWeight: 200,
    padding: "0 10px",
    borderBottom: "1px dotted #ddd",
  };

  const headerStyle: React.CSSProperties = {
    ...cellStyle,
    fontWeight: "bold",
    backgroundColor: "#f0f0f0",
    borderBottom: "1px dotted #ccc",
  };

  const canPrev = page > 0 && pageCount > 1;
  const canNext = pageCount > 1 && page < pageCount - 1;

  const handlePrev = async () => {
    if (!hasPaging || !canPrev || loading) return;
    const nextPage = Math.max(page - 1, 0);
    // ✅ update page; effect will load, but we also can eagerly load to feel snappier
    setPage(nextPage);
    await loadPage(nextPage);
  };

  const handleNext = async () => {
    if (!hasPaging || !canNext || loading) return;
    const nextPage = Math.min(page + 1, pageCount - 1);
    setPage(nextPage);
    await loadPage(nextPage);
  };

  return (
    <>
      <CCard
        style={{
          height: "35px",
          marginBottom: "4px",
          marginTop: "2px",
          border: "none",
          backgroundColor: "#f3f6f8",
          boxShadow: "none",
          borderRadius: "4px",
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: "0.25rem 0.5rem",
            height: "100%",
            backgroundColor: "transparent",
            justifyContent: "space-between",
          }}
        >
          <p style={{ margin: 0, fontSize: "0.78rem", fontWeight: 500 }}>General</p>

          <div style={{ display: "flex", gap: 10, alignItems: "center" }}>
            {vendorReceivedFromTotal !== undefined && (
              <span style={{ fontSize: "0.72rem", opacity: 0.85 }}>
                Received Count: {vendorReceivedFromTotal}
              </span>
            )}
            {vendorForReceivedFromTotal !== undefined && (
              <span style={{ fontSize: "0.72rem", opacity: 0.85 }}>
                Available Total: {vendorForReceivedFromTotal}
              </span>
            )}
            {vendorForSentToTotal !== undefined && (
              <span style={{ fontSize: "0.72rem", opacity: 0.85 }}>
                SentTo Total: {vendorForSentToTotal}
              </span>
            )}
          </div>
        </CCardBody>
      </CCard>

      <CCard style={{ height: "320px", marginBottom: "4px", marginTop: "15px" }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: "0.25rem 0.5rem", height: "100%" }}>
          <div style={{ width: "100%", height: "100%" }}>
            <div style={{ display: "flex", flexDirection: "column", height: "100%" }}>
              {/* Grid Table */}
              <div
                style={{
                  flex: 1,
                  display: "grid",
                  gridTemplateColumns: "1fr",
                  rowGap: "0px",
                  columnGap: "4px",
                  minHeight: "250px",
                  alignContent: "start",
                }}
              >
                <div style={headerStyle}>Name</div>

                {rows.length > 0 ? (
                  rows.map((item: any, index: number) => (
                    <div key={`${item.__id || index}-${index}`} style={cellStyle} title={item.__id || ""}>
                      {item.__displayName || <em style={{ color: "#6b7280" }}>(no name)</em>}
                      {item.__q ? " (Q)" : ""}
                    </div>
                  ))
                ) : (
                  <Typography
                    sx={{
                      gridColumn: `span ${COLUMNS}`,
                      fontSize: "0.75rem",
                      padding: "0 16px",
                      color: "text.secondary",
                    }}
                  >
                    {loading ? "Loading…" : "xxxx - xxxx"}
                  </Typography>
                )}
              </div>

              {/* ✅ Pagination (server-side) */}
              <div
                style={{
                  marginTop: "16px",
                  display: "flex",
                  justifyContent: "space-between",
                  alignItems: "center",
                }}
              >
                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: "0.7rem", padding: "2px 8px", minWidth: "unset", textTransform: "none" }}
                  onClick={handlePrev}
                  disabled={!hasPaging || !canPrev || loading}
                >
                  ◀ Previous
                </Button>

                <Typography fontSize="0.75rem">
                  Page {hasPaging ? page + 1 : hasData ? 1 : 0} of {hasPaging ? pageCount : hasData ? 1 : 0}
                  {loading ? " (Loading...)" : ""}
                </Typography>

                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: "0.7rem", padding: "2px 8px", minWidth: "unset", textTransform: "none" }}
                  onClick={handleNext}
                  disabled={!hasPaging || !canNext || loading}
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
