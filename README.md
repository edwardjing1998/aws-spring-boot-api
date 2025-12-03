const handleDuplicate = async () => {
    const clientId = (
      selectedGroupRow?.client ??
      selectedData?.client ??
      ''
    )
      .toString()
      .trim();
    const source = (selectedData?.sysPrin ?? '')
      .toString()
      .trim();
    const target = (targetSysPrin ?? '').toString().trim();

    if (!clientId || !source || !target) {
      alert(
        'Client, Source SysPrin, and Target SysPrin are required.',
      );
      return;
    }
    if (source === target) {
      alert(
        'Target SysPrin must be different from Source SysPrin.',
      );
      return;
    }

    // UPDATED: Using state variables copyAreas and copyVendorSentTo
    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(
        clientId,
      )}` +
      `/sysprins/${encodeURIComponent(source)}` +
      `/duplicate-to/${encodeURIComponent(target)}` +
      `?overwrite=false&copyAreas=${copyAreas}&copyVendorSentTo=${copyVendorSentTo}`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'POST',
        headers: { accept: '*/*' },
        body: '',
      });

      if (!res.ok) {
        let msg = `Duplicate failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      const scalarPatch = getCopyPatchFromSelectedData();
      const vendorPatch = getSourceVendorPatch();

      const fullPatch: any = {
        ...scalarPatch,
        ...vendorPatch,
        client: clientId,
        sysPrin: target,
        id: { client: clientId, sysPrin: target },
      };

      setSelectedData((prev: any) => ({
        ...(prev ?? {}),
        ...fullPatch,
      }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(target, fullPatch, clientId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          if (!prev) return prev;

          const prevSysPrins = Array.isArray(prev.sysPrins)
            ? prev.sysPrins
            : [];

          const exists = prevSysPrins.some((sp: any) => {
            const code = sp?.id?.sysPrin ?? sp?.sysPrin;
            const client = sp?.id?.client ?? sp?.client;
            return code === target && client === clientId;
          });

          const nextSysPrins = exists
            ? prevSysPrins.map((sp: any) => {
                const code = sp?.id?.sysPrin ?? sp?.sysPrin;
                const client = sp?.id?.client ?? sp?.client;
                if (code === target && client === clientId) {
                  return { ...sp, ...fullPatch };
                }
                return sp;
              })
            : [...prevSysPrins, fullPatch];

          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      alert('Sys/Prin duplicated successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to duplicate Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };
