import { Button, Typography } from "@mui/material";
import React, { useEffect, useMemo, useState } from "react";
import { CCard, CCardBody } from "@coreui/react";
import { asArray, asNumber } from "../utils/safe";
import type { SysPrinDto, VendorLinkDto } from "../types/traceDtos";

const PAGE_SIZE = 10;
const COLUMNS = 4;

type Props = { sysPrin?: SysPrinDto | null };

export default function PreviewFilesReceivedFrom({ sysPrin }: Props) {
  const [page, setPage] = useState(0);

  const rows = useMemo(() => {
    const arr = asArray<VendorLinkDto>(sysPrin?.vendorReceivedFrom);

    return arr.map((it) => {
      const id = it.vendorId ?? it.vendor?.id ?? "";
      const name = it.vendor?.name ?? "";
      const displayId = String(id ?? "").trim();
      const displayName = String(name ?? "").trim() || displayId;

      const q = !!it.queForMail;

      return { ...it, __id: displayId, __displayName: displayName, __q: q };
    });
  }, [sysPrin]);

  const total = asNumber(sysPrin?.vendorForReceivedFromTotal, 0);

  const pageCount = Math.ceil(rows.length / PAGE_SIZE) || 0;
  const pageData = rows.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);
  const hasData = rows.length > 0;

  useEffect(() => {
    if (page > 0 && page >= pageCount) setPage(0);
  }, [page, pageCount]);

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

  return (
    <>
      <CCard style={{ height: 35, marginBottom: 4, marginTop: 2, border: "none", backgroundColor: "#f3f6f8", boxShadow: "none", borderRadius: 4 }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: "0.25rem 0.5rem", height: "100%", backgroundColor: "transparent" }}>
          <p style={{ margin: 0, fontSize: "0.78rem", fontWeight: 500 }}>
            Received From (O) — Total Available: {total}
          </p>
        </CCardBody>
      </CCard>

      <CCard style={{ height: 320, marginBottom: 4, marginTop: 15 }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: "0.25rem 0.5rem", height: "100%" }}>
          <div style={{ width: "100%", height: "100%" }}>
            <div style={{ display: "flex", flexDirection: "column", height: "100%" }}>
              <div style={{ flex: 1, display: "grid", gridTemplateColumns: "1fr", rowGap: 0, columnGap: 4, minHeight: 250, alignContent: "start" }}>
                <div style={headerStyle}>Name</div>

                {pageData.length > 0 ? (
                  pageData.map((item: any, index: number) => (
                    <div key={`${item.__id || index}-${index}`} style={cellStyle} title={item.__id || ""}>
                      {item.__displayName || <em style={{ color: "#6b7280" }}>(no name)</em>}
                      {item.__q ? " (Q)" : ""}
                    </div>
                  ))
                ) : (
                  <Typography sx={{ gridColumn: `span ${COLUMNS}`, fontSize: "0.75rem", padding: "0 16px", color: "text.secondary" }}>
                    (no vendorReceivedFrom)
                  </Typography>
                )}
              </div>

              <div style={{ marginTop: 16, display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                <Button variant="text" size="small" sx={{ fontSize: "0.7rem", padding: "2px 8px", minWidth: "unset", textTransform: "none" }}
                  onClick={() => setPage((p) => Math.max(p - 1, 0))}
                  disabled={!hasData || page === 0}
                >
                  ◀ Previous
                </Button>

                <Typography fontSize="0.75rem">
                  Page {hasData ? page + 1 : 0} of {hasData ? pageCount : 0}
                </Typography>

                <Button variant="text" size="small" sx={{ fontSize: "0.7rem", padding: "2px 8px", minWidth: "unset", textTransform: "none" }}
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
}
