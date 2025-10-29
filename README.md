// after `const response = await fetch(...)`
const ct = response.headers.get('content-type') || '';
const saved = ct.includes('application/json')
  ? await response.json()
  : { ...payload, _message: await response.text() };

// local + parent updates
setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
onClientUpdated?.(saved);
