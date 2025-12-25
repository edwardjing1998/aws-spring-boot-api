const handleCreate = async () => {
  if (!draft.prefix || draft.atmCashRule === '') {
    setStatus({ severity: 'warning', text: 'Please enter both Prefix and ATM/Cash Rule.' });
    return;
  }

  try {
    setSubmitting(true);
    setStatus({ severity: 'info', text: 'Creating…' });

    // ✅ service returns void, so don't capture "created"
    await createAtmCashPrefix(draft);

    setStatus({ severity: 'success', text: 'Prefix created successfully.' });

    // ✅ build createdRow from draft
    const createdRow: AtmCashPrefixRow = {
      billingSp: draft.billingSp ?? '',
      prefix: draft.prefix ?? '',
      atmCashRule: String(draft.atmCashRule ?? ''),
    };

    onCreated?.(createdRow);
  } catch (err: any) {
    console.error('Create failed:', err);
    setStatus({ severity: 'error', text: `Create failed: ${err.message}` });
  } finally {
    setSubmitting(false);
  }
};
