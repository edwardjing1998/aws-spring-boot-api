import React, { useEffect, useMemo, useState, useRef } from 'react'
import { CCard, CCardBody } from '@coreui/react'
import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'
import { Button } from '@mui/material'

import './ZipCodeConfig.scss'
import { zipCodeTable, usStates } from './data.js'
import ActionCell from './ActionCell.js'
import ZipcodeTableDialog from './ZipCodeEditDialog.js'

ModuleRegistry.registerModules([AllCommunityModule])

const ZipcodeConfig = () => {
  const [rowData, setRowData] = useState([])
  const [selectedKey, setSelectedKey] = useState(null)
  const gridApiRef = useRef(null)

  // dialog state
  const [zipDialogOpen, setZipDialogOpen] = useState(false)
  const [zipDialogInitial, setZipDialogInitial] = useState(null)
  const [zipDialogMode, setZipDialogMode] = useState('addEdit') // 'addEdit' | 'delete' | 'review'
  const [zipDialogTitle, setZipDialogTitle] = useState('Zip Code Edit')

  // Build map "TX" -> "Texas"
  const stateMap = useMemo(() => {
    const map = new Map()
    usStates.forEach(({ acronym, stateName }) => map.set(acronym.toUpperCase(), stateName))
    return map
  }, [])

  useEffect(() => {
    const allRows = Array.isArray(zipCodeTable) ? zipCodeTable : Object.values(zipCodeTable).flat()
    setRowData(allRows)
  }, [])

  const formatState = (abbr) => {
    if (!abbr) return ''
    const up = String(abbr).toUpperCase().trim()
    const name = stateMap.get(up)
    return name ? `${name} - ${up}` : `Unknown - ${up}`
  }

  // ——— open dialog helpers ———
  const openZipDialogWithRow = (row) => {
    setZipDialogInitial({
      zip: String(row?.zipCode ?? ''),
      city: row?.city ?? '',
      state: row?.state ?? '',
    })
    setZipDialogMode('addEdit')
    setZipDialogTitle('Zip Code Edit')
    setZipDialogOpen(true)
  }

  const openZipDialogCreate = () => {
    setZipDialogInitial({ zip: '', city: '', state: '' })
    setZipDialogMode('addEdit')
    setZipDialogTitle('Zip Code Create')   // New
    setZipDialogOpen(true)
  }

  const openZipDialogReview = (row) => {
    setZipDialogInitial({
      zip: String(row?.zipCode ?? ''),
      city: row?.city ?? '',
      state: row?.state ?? '',
    })
    setZipDialogMode('review')
    setZipDialogTitle('Zip Code Review')   // Review
    setZipDialogOpen(true)
  }

  const openZipDialogDelete = (row) => {
    setZipDialogInitial({
      zip: String(row?.zipCode ?? ''),
      city: row?.city ?? '',
      state: row?.state ?? '',
    })
    setZipDialogMode('delete')
    setZipDialogTitle('Delete Zip Code')   // Delete
    setZipDialogOpen(true)
  }

  const [colDefs] = useState([
    { field: 'zipCode', headerName: 'Zip Code', flex: 20, minWidth: 120 },
    { field: 'city', headerName: 'City', flex: 20, minWidth: 120 },
    {
      headerName: 'State',
      field: 'state',
      flex: 30,
      minWidth: 160,
      valueGetter: (p) => formatState(p.data?.state),
      comparator: (a, b) => a.localeCompare(b),
    },
    {
      headerName: 'Action',
      field: 'action',
      flex: 30,
      minWidth: 200,
      sortable: false,
      filter: false,
      cellClass: ['action-cell', 'ag-cell-center'],
      cellRenderer: ActionCell,
      cellRendererParams: {
        onEdit: openZipDialogWithRow,
        onReview: openZipDialogReview,
        onDelete: openZipDialogDelete,
      },
    },
  ])

  const defaultColDef = {
    filter: true,
    floatingFilter: false,
    sortable: true,
    resizable: true,
    cellClass: 'ag-cell-center',
    headerClass: 'ag-header-center',
  }

  return (
    <div className="daily-activity-wrapper">
      <CCard>
        <CCardBody>

          {/* Top toolbar */}
          <div style={{
            display: 'flex',
            justifyContent: 'space-between',
            alignItems: 'center',
            marginBottom: 10,
            gap: 8,
          }}>
            <div style={{ fontWeight: 600 }}>&nbsp;</div>
            <Button variant="contained" size="small" onClick={openZipDialogCreate}>
              New
            </Button>
          </div>

          <div style={{ display: 'flex', justifyContent: 'center' }}>
            <div
              className="ag-grid-container ag-theme-quartz custom-compact no-vertical-borders"
              style={{
                height: '600px',
                width: '100%',
                '--ag-font-size': '12px',
                '--ag-row-height': '28px',
                '--ag-header-height': '30px',
                '--ag-grid-size': '2px',
                '--ag-header-background-color': '#DAEDF0',
              }}
            >
              <AgGridReact
                rowData={rowData}
                columnDefs={colDefs}
                defaultColDef={defaultColDef}
                pagination
                paginationPageSize={15}
                getRowId={({ data }) => String(data.zipCode)}
                rowSelection="single"
                rowClassRules={{
                  'row-selected-persist': (params) =>
                    selectedKey != null && String(params.data?.zipCode) === String(selectedKey),
                }}
                onRowClicked={(e) => setSelectedKey(String(e.data?.zipCode))}
                onGridReady={(e) => { gridApiRef.current = e.api }}
              />
            </div>
          </div>

        </CCardBody>
      </CCard>

      {/* Dialog mounts here */}
      <ZipcodeTableDialog
        open={zipDialogOpen}
        onClose={() => setZipDialogOpen(false)}
        initialData={zipDialogInitial}
        mode={zipDialogMode}      // 'addEdit' | 'delete' | 'review'
        title={zipDialogTitle}    // dynamic title
      />
    </div>
  )
}

export default ZipcodeConfig





import React, { useEffect, useState } from 'react';
import PropTypes from 'prop-types';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  IconButton,
} from '@mui/material';
import { CFormInput, CFormSelect } from '@coreui/react';
import {
  fetchAllStates,
  fetchZipCodeDetails,
  addZipCode,
  updateZipCode,
  deleteZipCode,
} from '../../../services/AdminEditService/ZipCodeTableService';
import { Zipcode, CloseIcon } from '../../../assets/brand/svg-constants';
import '../../../scss/zipCodeTable.scss';
import CustomSnackbar from '../../../components/CustomSnackbar';

const ZipcodeTableDialog = ({
  open,
  onClose,
  initialData,
  mode = 'addEdit',   // 'addEdit' | 'delete' | 'review'
  title,              // optional override for DialogTitle
}) => {
  const isDelete = mode === 'delete';
  const isReview = mode === 'review';

  const [formData, setFormData] = useState({ zip: '', city: '', state: '' });
  const [originalData, setOriginalData] = useState({ zip: '', city: '', state: '' });
  const [stateList, setStateList] = useState([]);
  const [isNewZipCode, setIsNewZipCode] = useState(false);
  const [enableDelete, setEnableDelete] = useState(false);

  // 'none' | 'delete' | 'add' | 'update' | 'delete-confirmation' | 'info'
  const [snackbarType, setSnackbarType] = useState('none');

  // Title priority: explicit prop > mode-based default
  const computedTitle =
    title ??
    (isReview
      ? 'Zip Code Review'
      : isDelete
      ? 'Delete Zip Code'
      : initialData && initialData.zip
      ? 'Zip Code Edit'
      : 'Zip Code Create');

  // Load states + seed form when dialog opens
  useEffect(() => {
    if (!open) return;

    (async () => {
      try {
        const response = await fetchAllStates();
        setStateList(response?.response?.deliveryAreaList || []);
      } catch {}
    })();

    if (initialData) {
      setFormData({ ...initialData });
      setOriginalData({ ...initialData });
      setIsNewZipCode(!initialData.zip);
      setEnableDelete(Boolean(initialData.zip));
    } else {
      setFormData({ zip: '', city: '', state: '' });
      setOriginalData({ zip: '', city: '', state: '' });
      setIsNewZipCode(true);
      setEnableDelete(false);
    }
  }, [open, initialData]);

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    clearForm();
    onClose?.();
  };

  const clearForm = () => {
    setFormData({ zip: '', city: '', state: '' });
    setOriginalData({ zip: '', city: '', state: '' });
    setIsNewZipCode(true);
    setEnableDelete(false);
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({ ...prev, [name]: value }));
  };

  // Prefill from server when a ZIP is entered (add/edit only)
  const tryPrefillFromZip = async () => {
    if (!formData.zip || isDelete || isReview) return;
    try {
      const response = await fetchZipCodeDetails(formData.zip);
      const found = response?.response?.ZipCode?.[0];
      if (found) {
        setIsNewZipCode(false);
        setFormData(found);
        setOriginalData(found);
        setEnableDelete(true);
      }
    } catch (error) {
      if (error?.response?.status === 404) {
        // New ZIP
        setIsNewZipCode(true);
        setEnableDelete(false);
        setOriginalData({ zip: formData.zip, city: '', state: '' });
      }
    }
  };

  // Save (add/edit only)
  const handleSave = async () => {
    if (isDelete || isReview) return;
    try {
      if (isNewZipCode) {
        await addZipCode(formData);
        setSnackbarType('add');
      } else {
        await updateZipCode(formData);
        setSnackbarType('update');
      }
      onClose?.();
    } catch (error) {}
  };

  // Delete flow
  const openDeleteConfirm = () => setSnackbarType('delete');

  const handleDeleteConfirm = async () => {
    setSnackbarType('none');
    try {
      await deleteZipCode(formData.zip, formData.city);
      setSnackbarType('delete-confirmation');
      clearForm();
      onClose?.();
    } catch (error) {}
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  // Enablement
  const allFieldsFilled =
    /^\d{5}$/.test(formData.zip) &&
    formData.city.trim() !== '' &&
    formData.state.trim() !== '';

  const changed =
    formData.zip !== originalData.zip ||
    formData.city !== originalData.city ||
    formData.state !== originalData.state;

  const saveEnabled = !isDelete && !isReview && (isNewZipCode ? allFieldsFilled : changed);

  return (
    <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'zip-code-table-dialog' }}>
      <DialogTitle>
        <div className="zip-code-icon"><Zipcode /></div>
        {computedTitle}
      </DialogTitle>

      <IconButton aria-label="close" onClick={handleClose}>
        <CloseIcon />
      </IconButton>

      <DialogContent dividers>
        {isDelete && (
          <div style={{ marginBottom: 12 }}>
            Are you sure you want to delete this ZIP code? This action cannot be undone.
          </div>
        )}

        <div className="zipcode-dialog-content">
          <div className="first-row">
            <div className="details">
              <span className="zipcode-content-text">Zip Code</span>
              <CFormInput
                name="zip"
                value={formData.zip}
                onChange={handleChange}
                onBlur={tryPrefillFromZip}
                onKeyDown={(e) => {
                  if (e.key === 'Enter') tryPrefillFromZip();
                }}
                className="zipcode-textbox"
                disabled={isDelete || isReview}
              />
            </div>

            <div className="details">
              <span className="zipcode-content-text">City</span>
              <CFormInput
                name="city"
                value={formData.city}
                onChange={handleChange}
                className="zipcode-textbox"
                disabled={isDelete || isReview}
              />
            </div>
          </div>

          <div className="details">
            <span className="zipcode-content-text">State</span>
            <CFormSelect
              name="state"
              value={formData.state}
              onChange={handleChange}
              className="zipcode-textbox"
              disabled={isDelete || isReview}
            >
              <option value="" disabled>
                Select State
              </option>
              {stateList.map((s) => (
                <option key={s.area} value={s.area}>
                  {s.name}
                </option>
              ))}
            </CFormSelect>
          </div>
        </div>
      </DialogContent>

      <DialogActions>
        <div className="zipcode-button-container">
          <Button variant="outlined" size="small" onClick={handleClose}>
            {isReview ? 'Close' : 'Cancel'}
          </Button>

          {isDelete ? (
            <Button variant="contained" size="small" color="error" onClick={openDeleteConfirm}>
              Confirm
            </Button>
          ) : (
            <>
              {/* Delete button in add/edit when an existing zip is loaded */}
              {!isReview && (
                <Button
                  variant="outlined"
                  size="small"
                  disabled={!enableDelete}
                  onClick={openDeleteConfirm}
                >
                  Delete
                </Button>
              )}
              {!isReview && (
                <Button
                  variant="contained"
                  size="small"
                  onClick={handleSave}
                  disabled={!saveEnabled}
                >
                  Save
                </Button>
              )}
            </>
          )}
        </div>
      </DialogActions>

      {/* Delete confirmation snackbar */}
      <CustomSnackbar
        open={snackbarType === 'delete'}
        onClose={handleSnackbarCancel}
        handleOk={handleDeleteConfirm}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
        type="delete"
      />

      {(snackbarType === 'add' ||
        snackbarType === 'update' ||
        snackbarType === 'delete-confirmation') && (
        <CustomSnackbar
          type={snackbarType}
          open={snackbarType !== 'none'}
          handleOk={handleSnackbarCancel}
          onClose={handleSnackbarCancel}
          title="Zip Code Table"
          body={
            snackbarType === 'add'
              ? 'You have successfully Added a new zip code'
              : snackbarType === 'update'
              ? 'You have successfully Updated a zip code'
              : snackbarType === 'delete-confirmation'
              ? 'You have successfully Deleted a zip code'
              : ''
          }
        />
      )}
    </Dialog>
  );
};

ZipcodeTableDialog.propTypes = {
  open: PropTypes.bool.isRequired,
  onClose: PropTypes.func.isRequired,
  initialData: PropTypes.shape({
    zip: PropTypes.string,
    city: PropTypes.string,
    state: PropTypes.string,
  }),
  mode: PropTypes.oneOf(['addEdit', 'delete', 'review']),
  title: PropTypes.string,
};

export default ZipcodeTableDialog;
