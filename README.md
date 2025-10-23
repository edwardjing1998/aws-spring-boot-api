<TableCell sx={{ ...compactCellSx }} align="right">
  <CButton
    color="primary"
    variant="outline"       // white bg + blue border/text
    size="sm"
    onClick={handleAddArea}
    disabled={!isEditable}
    style={{
      backgroundColor: '#fff', // ensure white
      borderColor: '#1976d2',  // blue border
      color: '#1976d2',        // blue text
      padding: '2px 8px',      // smaller height/width
      lineHeight: 1.1,
      minHeight: 0,            // allow compact height
      borderWidth: '1px',
    }}
  >
    New
  </CButton>
</TableCell>
