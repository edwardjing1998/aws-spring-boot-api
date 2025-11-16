const [sysPrinInput, setSysPrinInput] = useState(() => selectedData?.sysPrin ?? '');


useEffect(() => {
  setSysPrinInput(selectedData?.sysPrin ?? '');
}, [selectedData?.sysPrin]);



<Box
  sx={{
    display: 'flex',
    justifyContent: 'space-between',
    alignItems: 'center',
    mt: '20px',
    backgroundColor: '#f3f6f8',
    height: '50px',
    width: '100%',
    px: 2,
    gap: 2,
  }}
>
  <input
    type="text"
    placeholder="Enter Sys/Prin Name"
    value={sysPrinInput}
    onChange={(e) => {
      setSysPrinInput(e.target.value);   // only update local state → smooth typing
    }}
    onBlur={() => {
      // when user leaves the field, sync to selectedData once
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        sysPrin: sysPrinInput,
      }));
    }}
    disabled={!isEditable && mode !== 'changeAll'}
    style={{
      fontSize: '0.9rem',
      fontWeight: 400,
      width: '30vw',
      height: '30px',
      border: '1px solid '#ccc',
      borderRadius: '4px',
      paddingLeft: '8px',
      backgroundColor: 'white',
    }}
  />
</Box>

