import React from 'react';
import Snackbar from '@mui/material/Snackbar';
import { CButton } from '@coreui/react';
import { SnackbarCancelIcon, SnackbarRemoveIcon, UpdateIcon, SnackbarSuccessIcon, SnackbarActionIcon } from '../assets/brand/svg-constants';
import '../scss/CustomSnackbar.scss';

export interface CustomSnackbarProps {
    type: 'delete' | 'update' | 'add' | 'delete-confirmation' | 'info' | string;
    open: boolean;
    onClose: (event?: React.SyntheticEvent | Event, reason?: string) => void;
    handleOk?: () => void;
    title: string | React.ReactNode;
    body: string | React.ReactNode;
}

const CustomSnackbar: React.FC<CustomSnackbarProps> = ({ type, open, onClose, handleOk, title, body }) => {
    return (
        <Snackbar
            open={open}
            anchorOrigin={{ vertical: 'bottom', horizontal: 'left' }}
            onClose={onClose}
            className={
                type === 'delete'
                    ? 'delete-br-color'
                    : (type === 'update' || type === 'add' || type === 'delete-confirmation')
                        ? 'success-br-color'
                        : type === 'info'
                            ? 'info-br-color'
                            : ''
            }
            message={
                <>
                    <div className='snackbar-container'>
                        <div className='title-container'>
                            <div className='remove-cancel-container'>
                                {type === 'delete' && <SnackbarRemoveIcon />}
                                {(type === 'update' || type === 'add' || type === 'delete-confirmation') && <SnackbarSuccessIcon />}
                                {type === 'info' && <SnackbarActionIcon />}
                            </div>
                            <span className={`snackbar-title-text ${
                                type === 'delete'
                                    ? 'delete-title-color'
                                    : (type === 'update' || type === 'add' || type === 'delete-confirmation')
                                        ? 'success-title-color'
                                        : type === 'info'
                                            ? 'info-title-color'
                                            : ''
                            }`}>{title}</span>
                            <div className='cancel-container' onClick={(e) => onClose(e)}>
                                <SnackbarCancelIcon />
                            </div>
                        </div>
                        <div className='snackbar-body-content'>
                            {body}
                        </div>
                    </div>
                    <div className='snackbar-button-container'>
                        {type === 'delete' ? (
                            <>
                                <CButton className='cancel-button button-text' onClick={(e) => onClose(e)}>Cancel</CButton>
                                <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
                            </>
                        ) : (
                            <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
                        )}
                    </div>
                </>
            }
        />
    );
};

export default CustomSnackbar;





ERROR in src/Client/layout/CustomSnackbar.tsx:60:34
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    Type 'undefined' is not assignable to type 'Element | null'.
    58 |                         {type === 'delete' ? (
    59 |                             <>
  > 60 |                                 <CButton className='cancel-button button-text' onClick={(e) => onClose(e)}>Cancel</CButton>
       |                                  ^^^^^^^
    61 |                                 <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
    62 |                             </>
    63 |                         ) : (
ERROR in src/Client/layout/CustomSnackbar.tsx:61:34
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    59 |                             <>
    60 |                                 <CButton className='cancel-button button-text' onClick={(e) => onClose(e)}>Cancel</CButton>
  > 61 |                                 <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
       |                                  ^^^^^^^
    62 |                             </>
    63 |                         ) : (
    64 |                             <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
ERROR in src/Client/layout/CustomSnackbar.tsx:64:30
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    62 |                             </>
    63 |                         ) : (
  > 64 |                             <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
       |                              ^^^^^^^
    65 |                         )}
    66 |                     </div>
    67 |                 </>
