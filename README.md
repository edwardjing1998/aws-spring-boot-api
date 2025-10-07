// inside ClientInformationWindow.jsx

// 1) Define a factory for new mode only (can be inside the component)
const makeEmptyClient = () => ({
  client: '',
  name: '',
  addr: '',
  city: '',
  state: '',
  zip: '',
  contact: '',
  phone: '',
  billingSp: '',
  // add other fields you render
});

// 2) Keep your effect, but only seed on 'new'
useEffect(() => {
  if (mode === 'new') {
    setIsEditable(true);
    // only seed if not already set
    setSelectedGroupRow(prev => (prev && Object.keys(prev).length ? prev : makeEmptyClient()));
  } else if (mode === 'edit') {
    setIsEditable(true);
  } else {
    setIsEditable(false);
  }
}, [mode, setSelectedGroupRow]);

// 3) Use a safe view model so render never sees undefined
const viewRow = React.useMemo(
  () => (mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? {})),
  [mode, selectedGroupRow]
);

// 4) Bind inputs to viewRow (no crash in new mode)
<TextField
  value={viewRow.client ?? ''}
  onChange={(e) =>
    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), client: e.target.value }))
  }
  size="small"
  disabled={!isEditable}
/>

<TextField
  value={viewRow.name ?? ''}
  onChange={(e) =>
    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
  }
  size="small"
  fullWidth
  disabled={!isEditable}
/>
