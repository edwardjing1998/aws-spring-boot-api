import React, { useMemo } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';

/**
 * Props:
 *   - selectedData: {
 *       sysPrin?: string,
 *       vendorReceivedFrom?: Array<{ vendId, vendName, queueForMail }>,
 *       vendorSentTo?: Array<{ vendId, vendName, queueForMail }>,
 *       ...other fields
 *     }
 *   - selectedGroupRow?: any
 */
const PreviewSysPrinInformation = ({ selectedData }) => {
  // read directly from props
  const sysPrin = selectedData?.sysPrin ?? '';

  // If you really want memoization, make sure vendor arrays are in deps.
  const receivedFrom = useMemo(
    () => selectedData?.vendorReceivedFrom ?? [],
    [selectedData?.vendorReceivedFrom]
  );
  const sentTo = useMemo(
    () => selectedData?.vendorSentTo ?? [],
    [selectedData?.vendorSentTo]
  );

  return (
    <CRow>
      <CCol xs={12}>
        <CCard className="mb-3">
          <CCardBody style={{ padding: '10px' }}>
            <div style={{ fontWeight: 700, marginBottom: 8 }}>
              Sys/Prin: {sysPrin || '(n/a)'}
            </div>

            <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 16 }}>
              {/* File Received From */}
              <div style={{ border: '1px solid #eee', borderRadius: 6, padding: 10 }}>
                <div style={{ fontWeight: 600, marginBottom: 6 }}>
                  File Received From ({receivedFrom.length})
                </div>
                <div style={{ maxHeight: 220, overflowY: 'auto', fontSize: '0.85rem' }}>
                  {receivedFrom.length === 0 ? (
                    <div style={{ color: '#777' }}>No vendors</div>
                  ) : (
                    <ul style={{ margin: 0, paddingLeft: 16 }}>
                      {receivedFrom.map(v => (
                        <li key={v.vendId}>
                          {v.vendId} — {v.vendName}
                          {v.queueForMail ? ' (Q)' : ''}
                        </li>
                      ))}
                    </ul>
                  )}
                </div>
              </div>

              {/* File Sent To */}
              <div style={{ border: '1px solid #eee', borderRadius: 6, padding: 10 }}>
                <div style={{ fontWeight: 600, marginBottom: 6 }}>
                  File Sent To ({sentTo.length})
                </div>
                <div style={{ maxHeight: 220, overflowY: 'auto', fontSize: '0.85rem' }}>
                  {sentTo.length === 0 ? (
                    <div style={{ color: '#777' }}>No vendors</div>
                  ) : (
                    <ul style={{ margin: 0, paddingLeft: 16 }}>
                      {sentTo.map(v => (
                        <li key={v.vendId}>
                          {v.vendId} — {v.vendName}
                          {v.queueForMail ? ' (Q)' : ''}
                        </li>
                      ))}
                    </ul>
                  )}
                </div>
              </div>
            </div>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

// If you were using React.memo previously, keep it—but ensure the comparator
// *also* watches the vendor arrays so the preview re-renders when they change.
export default React.memo(
  PreviewSysPrinInformation,
  (prev, next) => {
    const prevSD = prev.selectedData || {};
    const nextSD = next.selectedData || {};
    return (
      prevSD.sysPrin === nextSD.sysPrin &&
      prevSD.vendorReceivedFrom === nextSD.vendorReceivedFrom &&
      prevSD.vendorSentTo === nextSD.vendorSentTo
    );
  }
);
