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
