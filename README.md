<TableContainer
  component={Paper}
  variant="outlined"
  sx={{
    maxHeight: 120,
    overflowY: 'auto',

    /* Firefox */
    scrollbarWidth: 'thin',
    scrollbarColor: '#cfd8dc #f5f7fa', // thumb color, track color

    /* Chrome/Edge/Safari */
    '&::-webkit-scrollbar': {
      width: 8,
      height: 8,
    },
    '&::-webkit-scrollbar-track': {
      backgroundColor: '#f5f7fa',
      borderRadius: 8,
    },
    '&::-webkit-scrollbar-thumb': {
      backgroundColor: '#cfd8dc',
      borderRadius: 8,
      border: '2px solid #f5f7fa', // creates a lighter ring
    },
    '&::-webkit-scrollbar-thumb:hover': {
      backgroundColor: '#bfcbd3',
    },
  }}
>
