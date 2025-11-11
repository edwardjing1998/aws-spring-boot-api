// 去重后的国家码列表（value=国家码，label=显示名称）
const DIAL_CODES = [
  { value: '+1',  label: 'US/CA' }, // 合并 +1
  { value: '+44', label: 'UK' },
  { value: '+61', label: 'AU' },
  { value: '+91', label: 'IN' },
];

<FormControl sx={{ minWidth: 90 }} size="small">
  <Select
    value={selectedGroupRow.phoneCountryCode || '+1'}
    onChange={handleChange('phoneCountryCode')}
    disabled={!isEditable}
    displayEmpty
    IconComponent={() => null} // 不显示下拉箭头
    renderValue={(val) => (DIAL_CODES.find(o => o.value === val)?.label || String(val))}
    sx={{
      ...sharedSx,
      width: 70,

      // 更矮的整体高度
      '& .MuiOutlinedInput-root': {
        height: 24,
        minHeight: 24,
      },

      // 文字垂直居中 & 紧凑内边距
      '& .MuiOutlinedInput-input': {
        paddingTop: 0,
        paddingBottom: 0,
        paddingLeft: 8,
        paddingRight: 8,      // 没有箭头就不需要大右内边距
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },
      '& .MuiSelect-select': {
        paddingTop: 0,
        paddingBottom: 0,
        minHeight: 0,
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },

      // 双保险：隐藏任何主题注入的箭头
      '& .MuiSelect-icon, & .MuiSelect-iconOutlined': { display: 'none' },
    }}
    MenuProps={{
      sx: {
        '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 26, lineHeight: 1.2 },
      },
      PaperProps: { style: { maxHeight: 260 } },
    }}
  >
    {DIAL_CODES.map(opt => (
      <MenuItem key={opt.value} value={opt.value}>
        {opt.label}
      </MenuItem>
    ))}
  </Select>
</FormControl>
