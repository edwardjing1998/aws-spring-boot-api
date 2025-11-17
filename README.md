      {mode === 'duplicate' || mode === 'changeAll' || mode === 'new' || mode === 'edit'  || mode === 'move' && (
      <Tabs
        value={tabIndex}
        onChange={(_, v) => setTabIndex(v)}
        variant="scrollable"
        scrollButtons="auto"
        sx={{ mt: 1, mb: 2 }}
      >
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>1</Box>General</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>2</Box>Remail Options</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>3</Box>Status Options</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />       
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>4</Box>File Received From</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>5</Box>File Sent To</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />       
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>6</Box>SysPrin Note</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />        
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>7</Box>Submission Overview</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
      </Tabs>
      )}
