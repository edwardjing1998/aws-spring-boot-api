import React, {
  FC,
  useState,
  useEffect,
} from 'react';
import Autocomplete from '@mui/material/Autocomplete';
import TextField from '@mui/material/TextField';
import Popper, { PopperProps } from '@mui/material/Popper';
import SearchIcon from '@mui/icons-material/Search';
import InputAdornment from '@mui/material/InputAdornment';

import {
  fetchClientSuggestions,
  fetchClientDetail,
} from './ClientIntegrationService';

// ---- Types for data coming from backend ----
export interface ClientSuggestion {
  client: string;
  name: string;
  // keep it open for extra fields from backend
  [key: string]: unknown;
}

export interface ClientDetail {
  client: string;
  name?: string;
  // your real shape is much richer; you can extend this later
  reportOptions?: unknown[];
  sysPrins?: unknown[];
  clientEmail?: unknown[];
  sysPrinsPrefixes?: unknown[];
  [key: string]: unknown;
}

// ---- Props for the component ----
export interface ClientAutoCompleteInputBoxProps {
  inputValue: string;
  setInputValue: (value: string) => void;
  onClientsFetched?: (clients: ClientSuggestion[]) => void;
  isWildcardMode: boolean;
  setIsWildcardMode?: (value: boolean) => void;
  onClientDetailLoaded?: (detail: ClientDetail) => void;
}

const ClientAutoCompleteInputBox: FC<ClientAutoCompleteInputBoxProps> = ({
  inputValue,
  setInputValue,
  onClientsFetched,
  isWildcardMode,
  setIsWildcardMode,
  onClientDetailLoaded,
}) => {
  const [options, setOptions] = useState<ClientSuggestion[]>([]);
  const [selectedValue, setSelectedValue] = useState<string | null>(null);

  const CustomPopper: FC<PopperProps> = (props) => (
    <Popper
      {...props}
      modifiers={[{ name: 'offset', options: { offset: [0, 4] } }]}
      style={{ width: 400 }}
    />
  );

  // reset input when wildcard mode is turned off
  useEffect(() => {
    if (!isWildcardMode) {
      setInputValue('');
    }
  }, [isWildcardMode, setInputValue]);

  // fetch suggestions as user types
  useEffect(() => {
    const delay = setTimeout(() => {
      const kw = inputValue.trim();
      if (!kw) {
        setOptions([]);
        return;
      }

      fetchClientSuggestions(kw)
        .then((data) => {
          // fetchClientSuggestions already normalizes to []
          const list = (data as ClientSuggestion[]) || [];
          setOptions(list);

          if (kw.endsWith('*') && typeof onClientsFetched === 'function') {
            onClientsFetched(list);
          }

          if (setIsWildcardMode) {
            setIsWildcardMode(kw.endsWith('*'));
          }
        })
        .catch((err) => {
          console.error('Autocomplete fetch error:', err);
          setOptions([]);
        });
    }, 300);

    return () => clearTimeout(delay);
  }, [inputValue, onClientsFetched, setIsWildcardMode]);

  // handle selection + call detail API + notify parent
  const handleChange = async (
    _event: React.SyntheticEvent<Element, Event>,
    value: string | null
  ) => {
    setSelectedValue(value);

    if (!value) return;

    // value is like "0003 - 0003 client name"
    const [clientCodeRaw] = value.split(' - ');
    const clientCode = clientCodeRaw?.trim();
    if (!clientCode) return;

    try {
      const detail = await fetchClientDetail(clientCode);
      // our TS version of fetchClientDetail already normalizes to a single object,
      // but we keep this fallback just in case
      const first: ClientDetail | null = Array.isArray(detail)
        ? (detail[0] as ClientDetail | undefined) ?? null
        : (detail as ClientDetail | null);

      if (first && typeof onClientDetailLoaded === 'function') {
        onClientDetailLoaded(first);
      }
    } catch (err) {
      console.error('Failed to fetch client detail:', err);
    }
  };

  return (
    <Autocomplete
      // T = string; multiple = false; disableClearable = false; freeSolo = true
      /* eslint-disable @typescript-eslint/no-explicit-any */
      // If you want fully strict typing, you can parametrize Autocomplete generics.
      /* eslint-enable @typescript-eslint/no-explicit-any */
      inputValue={inputValue}
      freeSolo
      disableClearable
      fullWidth
      options={options
        .map((opt) =>
          typeof opt === 'object' && opt.client && opt.name
            ? `${opt.client} - ${opt.name}`
            : ''
        )
        .filter(Boolean)}
      onInputChange={(_, v) => setInputValue(v)}
      onChange={handleChange}
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
