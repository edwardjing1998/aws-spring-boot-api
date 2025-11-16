    <Box
        sx={{
          display: 'flex', justifyContent: 'space-between', alignItems: 'center',
          mt: '20px', backgroundColor: '#f3f6f8', height: '50px', width: '100%', px: 2, gap: 2,
        }}
      >
        <input
          type="text"
          placeholder="Enter Sys/Prin Name"
          value={selectedData?.sysPrin || ''}
          onChange={(e) =>
            setSelectedData((prev) => ({
              ...prev,
              sysPrin: e.target.value,
            }))
          }
          disabled={!isEditable && mode !== 'changeAll'}
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
      </Box>
