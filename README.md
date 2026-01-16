                <TextField
                  value={name}
                  onChange={(e: ChangeEvent<HTMLInputElement>) => setName(e.target.value)}
                  disabled={!isEditable}
                  placeholder="Enter name"
                  style={{ fontSize: '0.73rem', width: 260, marginLeft: 0, height: '10px' }}
                  inputProps={{
                    maxLength: MAX.name,
                    'aria-describedby': 'name-counter',
                  }}
                />
