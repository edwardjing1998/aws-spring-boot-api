import React, { useState, useEffect, useRef, FC } from 'react';
import Autocomplete, { AutocompleteRenderInputParams } from '@mui/material/Autocomplete';
import TextField from '@mui/material/TextField';
import Popper, { PopperProps } from '@mui/material/Popper';
import SearchIcon from '@mui/icons-material/Search';
import InputAdornment from '@mui/material/InputAdornment';

// Assuming the service location based on the provided snippet. 
// You may need to adjust this path if the file structure differs.
import { fetchClientReportSuggestions } from '../views/sys-prin-configuration/utils/ClientReportIntegrationService';

// --- Types ---

export interface ClientReportSuggestion {
  reportId: number | string;
  name: string;
  fileExt?: string;
  [key: string]: any;
}

export interface ClientReportAutoCompleteInputBoxProps {
  inputValue: string;
  setInputValue: (value: string) => void;
  onClientsFetched?: (clients: ClientReportSuggestion[]) => void;
  isWildcardMode?: boolean;
  setIsWildcardMode?: (value: boolean) => void;
}

const ClientReportAutoCompleteInputBox: FC<ClientReportAutoCompleteInputBoxProps> = ({ 
  inputValue, 
  setInputValue, 
  onClientsFetched, 
  isWildcardMode, 
  setIsWildcardMode 
}) => {
  const [options, setOptions] = useState<ClientReportSuggestion[]>([]);
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [selectedValue, setSelectedValue] = useState<string | null>(null);
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const keywordRef = useRef<string>('');

  const CustomPopper: FC<PopperProps> = (props) => (
    <Popper
      {...props}
      modifiers={[{ name: 'offset', options: { offset: [0, 4] } }]}
      style={{ width: 400 }}
    />
  );

  useEffect(() => {
    if (!isWildcardMode) {
       // Only reset if not wildcard mode? Logic copied from source.
       // Note: original code was: if (!isWildcardMode) setInputValue('');
       // But useEffect dep was [isWildcardMode].
       // This resets input when toggling OFF wildcard mode.
       // setInputValue(''); // Uncomment if you want to clear input on mode switch
    }
  }, [isWildcardMode, setInputValue]);

  useEffect(() => {
    const delay = setTimeout(() => {
      const kw = inputValue.trim();
      if (!kw) {
        setOptions([]);
        return;
      }

      fetchClientReportSuggestions(kw)
        .then((data: any) => {
          const list: ClientReportSuggestion[] = data.data || data;
          setOptions(list);
          
          if (kw.endsWith('*') && typeof onClientsFetched === 'function') {
            onClientsFetched(list);
          }

          if (setIsWildcardMode) {
            setIsWildcardMode(kw.endsWith('*'));
          }
        })
        .catch((err: any) => {
          console.error('Autocomplete fetch error:', err);
          setOptions([]);
        });
    }, 300);

    return () => clearTimeout(delay);
  }, [inputValue, onClientsFetched, setIsWildcardMode]);  

  return (
    <Autocomplete
      inputValue={inputValue}
      freeSolo
      disableClearable
      fullWidth
      options={options
        .map((opt) =>
          typeof opt === 'object' && opt.reportId && opt.name ? `${opt.reportId} :::: ${opt.name}  :::: ${opt.fileExt}` : ''
        )
        .filter(Boolean)}
      onInputChange={(_, v) => setInputValue(v)}
      onChange={(_, v) => setSelectedValue(v)}
      PopperComponent={CustomPopper}
      slotProps={{
        paper: { sx: { fontSize: '0.78rem', width: 300 } },
        listbox: { sx: { fontSize: '0.78rem' } },
      }}
      sx={{ width: '100%', backgroundColor: '#fff' }}
      renderInput={(params: AutocompleteRenderInputParams) => (
        <TextField
          {...params}
          placeholder="Search Report"
          variant="outlined"
          fullWidth
          size="small"
          InputProps={{
            ...params.InputProps,
            type: 'search',
            endAdornment: (
              <InputAdornment position="end">
                <SearchIcon sx={{ fontSize: 18, color: '#555' }} />
              </InputAdornment>
            ),
            sx: {
              height: 36,
              p: '0 8px',
              fontSize: '0.875rem',
              backgroundColor: '#fff',
              '& .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
              '&:hover .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
              '&.Mui-focused .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
            },
          }}
        />
      )}
    />
  );
};

export default ClientReportAutoCompleteInputBox;
