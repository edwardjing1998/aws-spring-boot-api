  const handleCreate = async () => {
    if (!draft.prefix || draft.atmCashRule === '') {
      setStatus({ severity: 'warning', text: 'Please enter both Prefix and ATM/Cash Rule.' });
      return;
    }

    try {
      setSubmitting(true);
      setStatus({ severity: 'info', text: 'Creating…' });

      const created = await createAtmCashPrefix(draft); // ✅ capture response

      setStatus({ severity: 'success', text: 'Prefix created successfully.' });

      // ✅ prefer server response, fallback to draft
      const createdRow: AtmCashPrefixRow = {
        billingSp: (created?.billingSp ?? draft.billingSp ?? ''),
        prefix: (created?.prefix ?? draft.prefix ?? ''),
        atmCashRule: String(created?.atmCashRule ?? draft.atmCashRule ?? ''),
      };

      onCreated?.(createdRow);
    } catch (err: any) {
      console.error('Create failed:', err);
      setStatus({ severity: 'error', text: `Create failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  Property 'prefix' does not exist on type 'never'.ts(2339)
  Property 'billingSp' does not exist on type 'never'.ts(2339)
  Property 'atmCashRule' does not exist on type 'never'.ts(2339)



export const createAtmCashPrefix = async (data: AtmCashPrefixRow): Promise<void> => {
  const res = await fetch(`${CLIENT_SYSPRIN_WRITER_API_BASE}/prefix/add`, {
    method: 'POST',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({
      billingSp: data.billingSp ?? '',
      prefix: data.prefix ?? '',
      atmCashRule: String(data.atmCashRule ?? ''),
    }),
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
  }
};


  
