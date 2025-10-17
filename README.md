                <TextField
                value={form.emailBodyTx}
                onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
                size="small"
                fullWidth
                multiline
                minRows={3}
                maxRows={8} // grows to 8 rows, then scrolls
                helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
                inputProps={{
                    maxLength: 500,
                    style: { maxHeight: 160, overflowY: 'auto' }, // enforce scrollbar
                }}
                />
