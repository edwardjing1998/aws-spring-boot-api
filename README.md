                  <Box
                    sx={{
                      display: 'flex',
                      gap: 1,
                      alignItems: 'flex-start',
                      flex: 1,
                      maxWidth: 'calc(100% - 120px)',
                    }}
                  >
                    <TextField
                      label=""
                      value={selectedGroupRow.phone || ''}
                      onChange={handleChange('phone')}
                      size="small"
                      fullWidth
                      disabled={!isEditable}
                      sx={sharedSx}
                    />
                  </Box>
