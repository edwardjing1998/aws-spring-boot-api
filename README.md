import React, { useState, useEffect, useMemo } from 'react';
import {
  CRow,
  CCol,
  CCard,
  CCardBody
} from '@coreui/react';

import { Button } from '@mui/material';

import AutoCompleteInputBox from '../../../components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/PreviewSysPrinInformation';
import PreviewClientInformation from './PreviewClientInformation';
import { defaultSelectedData, mapRowDataToSelectedData } from './utils/SelectedData';
import NavigationPanel from './NavigationPanel';
import { fetchClientsPaging, fetchWildcardPage } from './utils/ClientIntegrationService';

import { Modal, Box } from '@mui/material';
import ClientInformationWindow from './utils/ClientInformationWindow';
import SysPrinInformationWindow from './utils/SysPrinInformationWindow';


const ClientInformationPage = () => {
  const [clientList, setClientList] = useState([]);
  const [currentPage, setCurrentPage] = useState(0); 
  const [inputValue, setInputValue] = useState('');
  const [isWildcardMode, setIsWildcardMode] = useState(false);
  const [selectedGroupRow, setSelectedGroupRow] = useState(null);
  const [selectedData, setSelectedData] = useState(defaultSelectedData);

  const [clientInformationWindow, setClientInformationWindow] = useState({ open: false, mode: 'edit' });
  const [sysPrinInformationWindow, setSysPrinInformationWindow] = useState({ open: false, mode: 'edit' });
  const [clientEditActionsDisabled, setClientEditActionsDisabled] = useState(true);

  useEffect(() => { 
    fetchClientsPaging(currentPage, 25)
      .then((data) => {
        setClientList(data);
      })
      .catch((error) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  const clientMap = useMemo(() => {
    const map = new Map();
    clientList.forEach(client => {
      map.set(client.billingSp, client);
    });
    return map;
  }, [clientList]);

  const handleClientsFetched = (fetchedClients) => {
    setCurrentPage(0);
    setClientList(prev => {
      const prevIds = prev.map(c => c.client).join(',');
      const newIds = fetchedClients.map(c => c.client).join(',');
      return prevIds === newIds ? prev : fetchedClients;
    });
  };

  const handleRowClick = (rowData) => {
    if (rowData.isGroup) {
      setClientEditActionsDisabled(false);
      setSelectedGroupRow(rowData);
      return;
    }

    const billingSp = rowData.billingSp || '';
    const matchedClient = clientMap.get(billingSp);
    const atmCashPrefixes = matchedClient?.sysPrinsPrefixes || [];
    const clientEmails = matchedClient?.clientEmail || [];
    const reportOptions = matchedClient?.reportOptions || [];
    const sysPrinsList = matchedClient?.sysPrins || [];

    const mappedData = mapRowDataToSelectedData(
      selectedData, rowData, atmCashPrefixes, clientEmails, reportOptions, sysPrinsList
    );
    setSelectedData(mappedData);
  };

  return (
    <div className="d-flex flex-column" style={{ minHeight: '100vh', width: '80vw', overflow: 'visible' }}>
      
      {/* Input + Buttons */}
      <CRow className="px-3" style={{ marginBottom: '10px' }}>
        <CCol style={{ flex: '0 0 29%', maxWidth: '29%', paddingLeft: '0px', border: 'none' }}>
          <AutoCompleteInputBox
            inputValue={inputValue}
            setInputValue={setInputValue}
            onClientsFetched={handleClientsFetched}
            isWildcardMode={isWildcardMode}
            setIsWildcardMode={setIsWildcardMode}
            sx={{
                '& .MuiOutlinedInput-root': {
                  '& fieldset': {
                    border: 'none',
                  },
                },
              }}
          />
        </CCol>

        <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', border: 'none', maxWidth: '71%' }}>
          {/* Main Content
          <BusinessIcon style={{ marginLeft: '8px', fontSize: '18px' }} />
           */}
          <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>Client Information</p>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'delete' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginLeft: 'auto', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Delete Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Edit Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none' }}
          >
            New Client
          </Button>
        </CCol>
      </CRow>

      {/* Main Content */}
      <CRow style={{ flexGrow: 1, paddingLeft: '0px', paddingRight: '12px' }}>
        {/* Navigation Panel */}
        <CCol style={{ flex: '0 0 30%', maxWidth: '30%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1200px', overflow: 'hidden' }}>
                <NavigationPanel
                  onRowClick={handleRowClick}
                  clientList={clientList}
                  setClientList={setClientList}
                  currentPage={currentPage}
                  setCurrentPage={setCurrentPage}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  onFetchWildcardPage={fetchWildcardPage}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* Client + SysPrin Info */}
        <CCol style={{ flex: '0 0 70%', maxWidth: '70%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1000px', overflow: 'hidden' }}>
                
                <CRow className="p-3" style={{ height: '400px' }}>
                  <CCol xs={12} style={{ height: '100%' }}>
                    <PreviewClientInformation
                      setClientInformationWindow={setClientInformationWindow}
                      selectedGroupRow={selectedGroupRow}
                    />
                  </CCol>
                </CRow>

                <CRow style={{ height: '30px', marginBottom: '10px' }}>
                <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '40%' }}>
                 {/* Main Content
                 <BusinessIcon style={{ marginLeft: '8px', fontSize: '18px' }} />
                 */}
                <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>SysPrin Information</p>
                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '60%' }}>
                <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>
                    
                             <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                Change All
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}
                              >
                                Edit SysPrin
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                New SysPrin
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                Duplicate
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                Move
                              </Button>

                </p>
                </CCol>
            </CRow>

            <CRow className="px-3" style={{ marginBottom: '20px', height: '500px' }}>
                <CCol xs={12} style={{ height: '100%' }}>
                    <CCard style={{ height: '100%' }}>
                    <CCardBody style={{ padding: '10px', height: '100%', overflowY: 'auto' }}>
                        <PreviewSysPrinInformation
                        setSysPrinInformationWindow={setSysPrinInformationWindow}
                        selectedData={selectedData}
                        selectedGroupRow={selectedGroupRow}
                        />
                    </CCardBody>
                    </CCard>
                </CCol>
            </CRow>

              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Drawers */}
          <Modal
              open={clientInformationWindow.open}
              onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
              aria-labelledby="client-info-modal"
              aria-describedby="client-info-modal-description"
            >
              <Box
                sx={{
                  position: 'absolute',
                  top: '50%',
                  left: '50%',
                  transform: 'translate(-50%, -50%)',
                  width: '860px',
                  height: '680px',
                  bgcolor: 'background.paper',
                  boxShadow: 24,
                  borderRadius: 2,
                  p: 2,
                  overflow: 'visible',
                  maxHeight: 'none',
                }}
              >
                <ClientInformationWindow
                  onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
                  selectedGroupRow={selectedGroupRow}
                  setSelectedGroupRow={setSelectedGroupRow}
                  mode={clientInformationWindow.mode}
                />
              </Box>
            </Modal>


            <Modal
                open={sysPrinInformationWindow.open}
                onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
                aria-labelledby="sysprin-info-modal"
                aria-describedby="sysprin-info-modal-description"
                >
                <Box
                    sx={{
                    position: 'absolute',
                    top: '50%',
                    left: '50%',
                    transform: 'translate(-50%, -50%)',
                    width: '960px',
                    maxHeight: '90vh',
                    bgcolor: 'background.paper',
                    boxShadow: 24,
                    borderRadius: 2,
                    p: 2,
                    overflowY: 'auto',
                    }}
                >
                    <SysPrinInformationWindow
                      onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
                      mode={sysPrinInformationWindow.mode}
                      selectedData={selectedData}
                      setSelectedData={setSelectedData}
                      selectedGroupRow={selectedGroupRow}
                    />
                </Box>
            </Modal>
    </div>
  );
};

export default ClientInformationPage;
