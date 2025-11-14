<CCol
              xs={3}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 45%',
                maxWidth: '45%',
              }}
            >
              {/* 🔴 HERE is the change you asked for */}
              <FormControlLabel
                control={<Checkbox size="small" checked={activeChecked} onChange={noop} disabled />}
                label={`SysPrin Active: ${activeChecked ? 'YES' : 'No'}`}
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  paddingLeft: '0px',
                  paddingRight: '0px',
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                }}
              />
            </CCol>
