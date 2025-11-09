// EditAtmCashPrefix.jsx
import React, { useEffect, useMemo, useState } from 'react';
import { Typography, IconButton, Button, Tooltip } from '@mui/material';
import { CRow, CCol } from '@coreui/react';
import VisibilityIcon from '@mui/icons-material/Visibility';
import EditIcon from '@mui/icons-material/Edit';
import DeleteIcon from '@mui/icons-material/Delete';

import AtmCashPrefixDetailWindow from './utils/AtmCashPrefixDetailWindow';

const PAGE_SIZE = 5;

const equalPrefixes = (a = [], b = []) =>
  a.length === b.length &&
  a.every((x, i) => {
    const y = b[i] || {};
    return (
      String(x.billingSp ?? '') === String(y.billingSp ?? '') &&
      String(x.prefix ?? '') === String(y.prefix ?? '') &&
      String(x.atmCashRule ?? '') === String(y.atmCashRule ?? '')
    );
  });

const EditAtmCashPrefix = ({ selectedGroupRow = {}, onDataChange }) => {
  const initial = Array.isArray(selectedGroupRow.sysPrinsPrefixes)
    ? selectedGroupRow.sysPrinsPrefixes
    : [];

  const [prefixes, setPrefixes] = useState(initial);
  const [selectedPrefix, setSelectedPrefix] = useState('');
  const [page, setPage] = useState(0);

  // Window state (handles detail/edit/delete/new)
  const [winOpen, setWinOpen] = useState(false);
  const [winMode, setWinMode] = useState('detail'); // 'detail' | 'edit' | 'delete' | 'new'
  const [winRow, setWinRow] = useState(null);

  // Push up helper
  const pushUp = (nextList) => {
    if (typeof onDataChange !== 'function') return;
    const existing = Array.isArray(selectedGroupRow.sysPrinsPrefixes)
      ? selectedGroupRow.sysPrinsPrefixes
      : [];
    if (equalPrefixes(existing, nextList)) return; // no-op, avoid loops
    onDataChange({ ...(selectedGroupRow ?? {}), sysPrinsPrefixes: nextList });
  };

  // keep local list in sync if parent changes groups or updates
  useEffect(() => {
    const next = Array.isArray(selectedGroupRow.sysPrinsPrefixes)
      ? selectedGroupRow.sysPrinsPrefixes
      : [];
    setPrefixes((prev) => (equalPrefixes(prev, next) ? prev : next));
    setPage(0);
    setSelectedPrefix('');
  }, [selectedGroupRow]);

  const pageCount = useMemo(
    () => Math.ceil((prefixes.length || 0) / PAGE_SIZE) || 0,
    [prefixes.length]
  );
  const pageData = useMemo(
    () => prefixes.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [prefixes, page]
  );

  const cellStyle = (isSelected) => ({
    backgroundColor: isSelected ? '#cce5ff' : 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 400,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
    cursor: 'pointer',
  });

  const headerStyle = {
    ...cellStyle(false),
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
    cursor: 'default',
  };

  const atmCashLabel = (rule) => {
    if (rule === '0' || rule === 0) return 'Destroy';
    if (rule === '1' || rule === 1) return 'Return';
    return 'N/A';
  };

  // Row selection (just for highlighting)
  const handleRowClick = (item) => setSelectedPrefix(item.prefix);

  // Open windows
  const openDetail = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('detail');
    setWinOpen(true);
  };
  const openEdit = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('edit');
    setWinOpen(true);
  };
  const openDelete = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('delete');
    setWinOpen(true);
  };
  const openNew = () => {
    setWinRow({ billingSp: selectedGroupRow?.billingSp || '', prefix: '', atmCashRule: '' });
    setWinMode('new');
    setWinOpen(true);
  };

  const isPrevDisabled = page <= 0;
  const isNextDisabled = pageCount === 0 || page >= pageCount - 1;

  return (
    <>
      <CRow className="mb-3">
        <CCol xs={12}>
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '5px' }}>
            <div className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
              <div style={{ width: '100%', height: '100%' }}>
                <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
                  <div
                    style={{
                      flex: 1,
                      display: 'grid',
                      gridTemplateColumns: '1fr 1fr 1fr 1fr',
                      rowGap: '0px',
                      columnGap: '4px',
                      minHeight: '250px',
                      alignContent: 'start',
                    }}
                  >
                    <div style={headerStyle}>Billing SP</div>
                    <div style={headerStyle}>Prefix</div>
                    <div style={headerStyle}>ATM/Cash</div>
                    <div style={headerStyle}>Action</div>

                    {pageData.map((item, index) => {
                      const isSelected = item.prefix === selectedPrefix;
                      return (
                        <React.Fragment key={`${item.billingSp}-${item.prefix}-${index}`}>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.billingSp}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.prefix}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {atmCashLabel(item.atmCashRule)}
                          </div>

                          {/* Action column */}
                          <div style={cellStyle(false)} onClick={(e) => e.stopPropagation()}>
                            <Tooltip title="Detail">
                              <IconButton size="small" aria-label="detail" onClick={(e) => openDetail(item, e)}>
                                <VisibilityIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Edit">
                              <IconButton size="small" aria-label="edit" onClick={(e) => openEdit(item, e)}>
                                <EditIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Delete">
                              <IconButton size="small" aria-label="delete" onClick={(e) => openDelete(item, e)}>
                                <DeleteIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                          </div>
                        </React.Fragment>
                      );
                    })}
                  </div>

                  {/* Pager */}
                  <div
                    style={{
                      marginTop: '0px',
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
                      disabled={isPrevDisabled}
                    >
                      ◀ Previous
                    </Button>
                    <Typography fontSize="0.75rem">
                      Page {prefixes.length > 0 ? page + 1 : 0} of {prefixes.length > 0 ? pageCount : 0}
                    </Typography>
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.min(p + 1, Math.max(pageCount - 1, 0)))}
                      disabled={isNextDisabled}
                    >
                      Next ▶
                    </Button>
                  </div>

                  {/* New button centered under pager */}
                  <div
                    style={{
                      marginTop: '8px',
                      display: 'flex',
                      justifyContent: 'center',
                      alignItems: 'center',
                    }}
                  >
                    <Button
                      onClick={openNew}
                      variant="contained"
                      color="primary"
                      size="small"
                      sx={{ textTransform: 'none', fontSize: '0.8rem', px: 2.5, py: 0.75 }}
                    >
                      New
                    </Button>
                  </div>

                </div>
              </div>
            </div>
          </div>
        </CCol>
      </CRow>

      {/* Unified Window */}
      <AtmCashPrefixDetailWindow
        open={winOpen}
        mode={winMode}
        row={winRow}
        onClose={() => setWinOpen(false)}
        onConfirm={async (_mode, draft) => {
          // EDIT result
          setPrefixes((prev) => {
            const idx = prev.findIndex(
              (r) => String(r.billingSp) === String(draft.billingSp) && String(r.prefix) === String(draft.prefix)
            );
            const next = [...prev];
            if (idx >= 0) {
              next[idx] = { ...next[idx], ...draft, atmCashRule: String(draft.atmCashRule ?? '') };
            } else {
              next.push({ ...draft, atmCashRule: String(draft.atmCashRule ?? '') });
            }
            if (!equalPrefixes(prev, next)) pushUp(next);
            return next;
          });
          setSelectedPrefix(draft.prefix);
        }}
        onCreated={(created) => {
          setPrefixes((prev) => {
            const exists = prev.some(
              (r) =>
                String(r.billingSp) === String(created.billingSp) &&
                String(r.prefix) === String(created.prefix)
            );
            const normalized = { ...created, atmCashRule: String(created.atmCashRule ?? '') };
            const next = exists
              ? prev.map((r) =>
                  String(r.billingSp) === String(created.billingSp) &&
                  String(r.prefix) === String(created.prefix)
                    ? normalized
                    : r
                )
              : [...prev, normalized];

            // Jump to the last page so the new row is visible (optional)
            const newPageCount = Math.ceil(next.length / PAGE_SIZE) || 0;
            setPage(Math.max(newPageCount - 1, 0));

            if (!equalPrefixes(prev, next)) pushUp(next);
            return next;
          });
          setSelectedPrefix(created.prefix);
        }}
        onDeleted={(deleted) => {
          setPrefixes((prev) => {
            const next = prev.filter(
              (r) =>
                !(
                  String(r.billingSp) === String(deleted.billingSp) &&
                  String(r.prefix) === String(deleted.prefix)
                )
            );

            const newPageCount = Math.ceil((next.length || 0) / PAGE_SIZE) || 0;
            setPage((p) => Math.min(p, Math.max(newPageCount - 1, 0)));

            setSelectedPrefix((pfx) => (pfx === deleted.prefix ? '' : pfx));

            if (!equalPrefixes(prev, next)) pushUp(next);
            return next;
          });
        }}
      />
    </>
  );
};

export default EditAtmCashPrefix;
