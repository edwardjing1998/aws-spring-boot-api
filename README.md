const MAX = { name: 60, addr: 120, city: 40 };

<TextField
  label="Name"
  value={form.name}
  onChange={e => setForm(f => ({...f, name: e.target.value.slice(0, MAX.name)}))}
  inputProps={{ maxLength: MAX.name }}
  helperText={`${form.name.length}/${MAX.name}`}
  error={Boolean(errors.name)}
  FormHelperTextProps={{ component: 'div' }}
/>

// show server error under the counter
{errors.name && <div style={{color:'#d32f2f', fontSize:'0.75rem'}}>{errors.name[0]}</div>}
