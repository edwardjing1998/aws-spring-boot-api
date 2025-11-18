const formatPhone = (value) => {
  // keep only digits
  const digits = value.replace(/\D/g, '').slice(0, 10);

  const part1 = digits.slice(0, 3);
  const part2 = digits.slice(3, 6);
  const part3 = digits.slice(6, 10);

  if (digits.length <= 3) return part1;
  if (digits.length <= 6) return `${part1}-${part2}`;
  return `${part1}-${part2}-${part3}`;
};



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
    onChange={(e) => {
      const formatted = formatPhone(e.target.value);
      setSelectedGroupRow((prev) => ({
        ...(prev ?? {}),
        phone: formatted,
      }));
    }}
    size="small"
    fullWidth
    disabled={!isEditable}
    sx={sharedSx}
    inputProps={{
      inputMode: 'numeric', // mobile keyboard hint
      maxLength: 12,        // xxx-xxx-xxxx
    }}
  />
</Box>
