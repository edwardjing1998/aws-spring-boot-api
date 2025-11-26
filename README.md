import React, { useState, useEffect, useRef } from 'react';
import Autocomplete from '@mui/material/Autocomplete';
import TextField from '@mui/material/TextField';
import Popper from '@mui/material/Popper';
import SearchIcon from '@mui/icons-material/Search';
import InputAdornment from '@mui/material/InputAdornment';

import {
  fetchClientSuggestions,
  fetchClientDetail, // 👈 NEW import
} from './ClientIntegrationService';

const ClientAutoCompleteInputBox = ({
  inputValue,
  setInputValue,
  onClientsFetched,
  isWildcardMode,
  setIsWildcardMode,
  onClientDetailLoaded, // 👈 NEW: parent callback to receive detail data
}) => {
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
  }, [isWildcardMode, setInputValue]);

  useEffect(() => {
    const delay = setTimeout(() => {
      const kw = inputValue.trim();
      if (!kw) {
        setOptions([]);
        return;
      }

      fetchClientSuggestions(kw)
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
  }, [inputValue, onClientsFetched, setIsWildcardMode]);

  // 👇 NEW: handle selection + call detail API + notify parent
  const handleChange = async (_, v) => {
    setSelectedValue(v);

    if (!v) return;

    // v is like "0003 - 0003 client name"
    const [clientCodeRaw] = v.split(' - ');
    const clientCode = clientCodeRaw?.trim();
    if (!clientCode) return;

    try {
      const detail = await fetchClientDetail(clientCode); // calls /detail/{client}/00000000
      // Your API returns an array with one object:
      // [ { client, name, ..., sysPrins: [...], reportOptions: [...], ... } ]
      const first = Array.isArray(detail) ? detail[0] : detail;

      if (first && typeof onClientDetailLoaded === 'function') {
        onClientDetailLoaded(first); // 👈 send detail up to parent
      }
    } catch (err) {
      console.error('Failed to fetch client detail:', err);
      // optional: you could surface error to parent with another callback
    }
  };

  return (
    <Autocomplete
      inputValue={inputValue}
      freeSolo
      disableClearable
      fullWidth
      options={options
        .map((opt) =>
          typeof opt === 'object' && opt.client && opt.name ? `${opt.client} - ${opt.name}` : '',
        )
        .filter(Boolean)}
      onInputChange={(_, v) => setInputValue(v)}
      onChange={handleChange} // 👈 use the new handler
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

export default ClientAutoCompleteInputBox;
