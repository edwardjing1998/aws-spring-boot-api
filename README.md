        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          <Button variant="contained" size="small" onClick={handlePrimaryClick} disabled={saving}>
            {mode === 'changeAll' ? 'Change All' : mode === 'duplicate' ? 'Duplicate' : mode === 'move' ? 'Move' : 'Create'}
          </Button>
          <Button variant="outlined" size="small" onClick={() => setTabIndex((i) => Math.min(i + 1, 6))}>
            Next
          </Button>
        </CCol>
