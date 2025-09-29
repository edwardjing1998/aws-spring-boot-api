import React, { useState, useEffect } from 'react'
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import IconButton from '@mui/material/IconButton';
import { CFormInput, CFormSelect } from '@coreui/react';
import { fetchAllStates, fetchZipCodeDetails, addZipCode, updateZipCode, deleteZipCode } from '../../../services/AdminEditService/ZipCodeTableService';
import { Zipcode, CloseIcon } from '../../../assets/brand/svg-constants';
import '../../../scss/zipCodeTable.scss';
import CustomSnackbar from '../../../components/CustomSnackbar';



const ZipcodeTableDialog = ({ open, onClose }) => {
  const [formData, setFormData] = useState({
    zip: '',
    city: '',
    state: '',
  })
  const [originalData, setOriginalData] = useState({
    zip: '',
    city: '',
    state: '',
  });
  const [stateList, setStateList] = useState([]);
  const [isNewZipCode, setIsNewZipCode] = useState(false);
  const [enableDelete, setEnableDelete] = useState(false);
  // snackbarType: 'none', 'delete', 'success', 'add', 'edit'
  const [snackbarType, setSnackbarType] = useState('none');

  /**
   * Effect to fetch all states when the dialog is opened.
   */
  useEffect(() => {
    if (open) {
      getAllStates();
    }
  }, [open]);

  /**
   * Handles input changes for all form fields.
   * Updates the formData state with the new value.
   * @param {object} e - The input change event.
   */
  const handleChange = (e) => {
    const { name, value } = e.target
    setFormData((prev) => ({ ...prev, [name]: value }))
  }

  /**
   * Fetches the list of all states from the API and updates stateList.
   * Called when the dialog is opened.
   */
  const getAllStates = async () => {
    try {
      const response = await fetchAllStates();
      setStateList(response?.response?.deliveryAreaList || []);
    } catch (error) {
    }
  };

  /**
   * Fetches details for the entered zip code from the API.
   * If found, populates the form with the data and enables delete.
   * If not found (404), prepares the form for a new zip code entry.
   */
  const getZipCodeDetails = async () => {
    if (formData.zip) {
      try {
        const response = await fetchZipCodeDetails(formData.zip);
        setIsNewZipCode(false);
        setFormData(response?.response?.ZipCode[0]);
        setOriginalData(response?.response?.ZipCode[0]);
        setEnableDelete(true);
      } catch (error) {
        if (error.response && error.response.status === 404) {
          setFormData({
            zip: formData.zip,
            city: '',
            state: '',
          });
          setOriginalData({
            zip: formData.zip,
            city: '',
            state: '',
          });
          setIsNewZipCode(true);
          setEnableDelete(false);
        } else {
          // Handle other errors
          console.error('Error fetching zip code details:', error);
        }
      }
    }
  };

  /**
   * Opens the delete confirmation snackbar.
   */
  const handleDelete = () => {
    setSnackbarType('delete');
  };

  /**
   * Handles the OK action in the delete confirmation snackbar.
   * Calls the delete API, clears the form, and shows the success snackbar.
   */
  const handleSnackbarOk = async () => {
    setSnackbarType('none');
    try {
      await deleteZipCode(formData.zip, formData.city);
      clearFormData();
      setSnackbarType('delete-confirmation');
    } catch (error) {
    }
  };

  /**
   * Handles the Cancel/Close action for any snackbar.
   * Resets the snackbarType to 'none'.
   */
  const handleSnackbarCancel = () => {
    setSnackbarType('none');
  };

  /**
   * Handles the Save button click.
   * Calls handleAdd for new zip codes, or handleUpdate for existing ones.
   */
  const handleSave = async () => {
    if (isNewZipCode) {
      await handleAdd();
    } else {
      await handleUpdate();
    }
  };

  /**
   * Calls the add API to add a new zip code.
   * Shows the add success snackbar and clears the form on success.
   */
  const handleAdd = async () => {
    try {
      const response = await addZipCode(formData);
      setSnackbarType('add');
      clearFormData();
    } catch (error) {
    }
  };

  /**
   * Calls the update API to update an existing zip code.
   * Shows the edit success snackbar and clears the form on success.
   */
  const handleUpdate = async () => {
    try {
      const response = await updateZipCode(formData);
      setSnackbarType('update');
      clearFormData();
    } catch (error) {
    }
  };

  /**
   * Handles closing the dialog.
   * Prevents closing on backdrop click or escape key.
   * Clears the form and calls the parent onClose.
   * @param {object} event - The close event.
   * @param {string} reason - The reason for closing.
   */
  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
      return;
    }
    clearFormData();
    onClose();
  };

  /**
   * Clears the form data and resets formData and originalData to initial values.
   */
  const clearFormData = () => {
    setFormData({
      zip: '',
      city: '',
      state: '',
    });
    setOriginalData({
      zip: '',
      city: '',
      state: '',
    });
  };


  // For new zip code, enable Save only if all fields are filled and zip code length is 5
  const allFieldsFilled =
    /^\d{5}$/.test(formData.zip) &&
    formData.city.trim() !== '' &&
    formData.state.trim() !== '';

  // Check if formData is different from originalData
  const isChanged = (
    formData.zip !== originalData.zip ||
    formData.city !== originalData.city ||
    formData.state !== originalData.state
  );

  let enableSave = false;
  enableSave = isNewZipCode ? allFieldsFilled : isChanged;

  return (
    <Dialog open={open}
      onClose={handleClose} PaperProps={{ className: 'zip-code-table-dialog' }}
    >
      <DialogTitle> <div className='zip-code-icon'><Zipcode /></div> Zip Code Edit</DialogTitle>
      <IconButton
        aria-label="close"
        onClick={handleClose}
      >
        <CloseIcon />
      </IconButton>
      <DialogContent dividers>
        <div className='zipcode-dialog-content'>
          <div className='first-row'>
            <div className='details'>
              <span className='zipcode-content-text'>Zip Code</span>
              <CFormInput
                name="zip"
                value={formData.zip}
                onChange={handleChange}
                onBlur={getZipCodeDetails}
                onKeyDown={(e) => { if (e.key === 'Enter') { getZipCodeDetails(); } }}
                className='zipcode-textbox'
              />
            </div>
            <div className='details'>
              <span className='zipcode-content-text'>City</span>
              <CFormInput name="city" value={formData.city} onChange={handleChange} className='zipcode-textbox' />
            </div>
          </div>
          <div className='details'>
            <span className='zipcode-content-text'>State</span>
            <CFormSelect name="state" value={formData.state} onChange={handleChange} className='zipcode-textbox'>
              <option value="" disabled>Select State</option>
              {stateList.map((state) => (
                <option key={state.area} value={state.area}>
                  {state.name}
                </option>
              ))}
            </CFormSelect>
          </div>
        </div>
      </DialogContent>
      <DialogActions>
        <div className='zipcode-button-container'>
          <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
          <Button variant="outlined" size="small" disabled={!enableDelete} onClick={handleDelete}>Delete</Button>
          <Button variant="contained" size="small" onClick={handleSave} disabled={!enableSave}>Save</Button>
        </div>
      </DialogActions>
      <CustomSnackbar
        open={snackbarType === 'delete'}
        onClose={handleSnackbarCancel}
        handleOk={handleSnackbarOk}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
        type="delete"
      />
      {(snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation') && (
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
    </Dialog >

  )
}

export default ZipcodeTableDialog
