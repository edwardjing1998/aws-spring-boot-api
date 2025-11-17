<Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 2 }}>
            <input
            type="text"
            placeholder="Enter Sys/Prin ID"
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
            disabled={!isEditable && (mode === 'changeAll' || mode === 'delete')}
            style={{
              fontSize: '0.9rem',
              fontWeight: 400,
              width: '30vw',
              height: '30px',
              border: '1px solid #ccc',
              borderRadius: '4px',
              paddingLeft: '8px',
              backgroundColor: 'white',
            }}
          />
          <TextField
            size="small"
            label="Old Client ID"
            value={oldClientId}
            onChange={(e) => setOldClientId(e.target.value)}
            InputProps={{ sx: { fontSize: '0.85rem' } }}
          />
          <TextField
            size="small"
            label="New Client ID"
            value={newClientId}
            onChange={(e) => setNewClientId(e.target.value)}
            InputProps={{ sx: { fontSize: '0.85rem' } }}
          />
        </Box>
