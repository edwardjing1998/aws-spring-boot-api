       <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Fax Number</label>
            <Box sx={{ display: 'flex', gap: 1 }}>
                
                {/* Country Code Select */}
                <FormControl sx={{ minWidth: 70 }} size="small">
                <Select
                    value={selectedGroupRow.phoneCountryCode || '+1'}
                    onChange={handleChange('phoneCountryCode')}
                    disabled={!isEditable}
                    displayEmpty
                    sx={{
                    ...sharedSx,
                    width: 70,
                    '& .MuiInputBase-root': {
                        height: '36px',
                        fontSize: '0.78rem',
                    },
                    '& .MuiSelect-select': {
                        paddingY: '6px',
                        fontSize: '0.78rem',
                    },
                    }}
                >
                    <MenuItem value="+1">🇺🇸 US</MenuItem>
                    <MenuItem value="+1-CA">🇨🇦 CA</MenuItem>
                    <MenuItem value="+44">🇬🇧 UK</MenuItem>
                    <MenuItem value="+61">🇦🇺 AU</MenuItem>
                    <MenuItem value="+91">🇮🇳 IN</MenuItem>
                </Select>
                </FormControl>

                {/* Phone Number Input */}
                <Box sx={{ display: 'flex', gap: 1, alignItems: 'flex-start' }}>
                <TextField
                    label=""
                    value={selectedGroupRow.faxNumber || ''}
                    onChange={handleChange('faxNumber')}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                />
                </Box>
            </Box>
            </FormControl>
