import React, { useState, useEffect, useRef } from 'react';
import Autocomplete from '@mui/material/Autocomplete';
import TextField from '@mui/material/TextField';
import Popper from '@mui/material/Popper';
import SearchIcon from '@mui/icons-material/Search';
import InputAdornment from '@mui/material/InputAdornment';

import { fetchClientReportSuggestions } from '../modules/edit/client-information/utils/ClientReportIntegrationService';

const ClientReportAutoCompleteInputBox = ({ inputValue, setInputValue, onClientsFetched, isWildcardMode, setIsWildcardMode }) => {
  const [options, setOptions] = useState([]);
  const [selectedValue, setSelectedValue] = useState(null);
  const keywordRef = useRef('');

  const CustomPopper = (props) => (
    <Popper
      {...props}
      modifiers={[{ name: 'offset', options: { offset: [0, 4] } }]}
      style={{ width: 400 }}
    />
  );

  useEffect(() => {
    if (!isWildcardMode) setInputValue('');
  }, [isWildcardMode]);

  useEffect(() => {
    const delay = setTimeout(() => {
      const kw = inputValue.trim();
      if (!kw) return setOptions([]);

      fetchClientReportSuggestions(kw)
        .then((data) => {
          const list = data.data || data;
          setOptions(list);
          if (kw.endsWith('*') && typeof onClientsFetched === 'function') {
            onClientsFetched(list);
          }

          setIsWildcardMode?.(kw.endsWith('*'));
        })
        .catch((err) => {
          console.error('Autocomplete fetch error:', err);
          setOptions([]);
        });
    }, 300);

    return () => clearTimeout(delay);
  }, [inputValue]);  

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
      renderInput={(params) => (
        <TextField
          {...params}
          placeholder="Search Client"
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
