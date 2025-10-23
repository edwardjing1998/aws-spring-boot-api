<TableBody
  sx={{
    '& .MuiTableRow-root': { height: 26 }, // your compact row height
    // make the body reserve space even with 0 rows
    '&:empty::after': {
      content: '""',
      display: 'block',
      height: '80px', // adjust to match your desired empty body height
    },
  }}
>
