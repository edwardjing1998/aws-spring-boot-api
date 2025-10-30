      onClientUpdated={(saved) => {
        if (!saved) return;

        // If the API sometimes returns text/thin objects, you may want to normalize first:
        const obj = typeof saved === 'object' && saved ? saved : { client: selectedGroupRow?.client, ...saved };

        // 1) Upsert into the left list
        setClientList((prev) => {
          const idx = prev.findIndex(
            (c) =>
              String(c?.client ?? '') === String(obj?.client ?? '') ||
              (obj?.billingSp && String(c?.billingSp ?? '') === String(obj?.billingSp ?? ''))
          );
          if (idx === -1) return prev;
          const next = [...prev];
          next[idx] = { ...next[idx], ...obj };
          return next;
        });

        // 2) Patch the preview card (preserve heavy slices if the update response is thin)
        setSelectedGroupRow((prev) => {
          if (!prev) return obj;
          return {
            ...prev,
            ...obj,
            clientEmail:       obj.clientEmail       ?? prev.clientEmail,
            sysPrins:          obj.sysPrins          ?? prev.sysPrins,
            sysPrinsPrefixes:  obj.sysPrinsPrefixes  ?? prev.sysPrinsPrefixes,
            reportOptions:     obj.reportOptions     ?? prev.reportOptions,
          };
        });
      }}
