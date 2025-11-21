if (!res.ok) {
  let msg = `Duplicate failed (${res.status})`;

  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) {
      const j = await res.json();
      msg = j?.message || JSON.stringify(j);
    } else {
      msg = await res.text();
    }
  } catch (err) {
    // Ignore parse errors; keep the default msg
  }

  throw new Error(msg);
}
