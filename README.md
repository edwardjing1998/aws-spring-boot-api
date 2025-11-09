const equalPrefixes = (a = [], b = []) =>
  a.length === b.length &&
  a.every((x, i) => {
    const y = (b[i] || {});
    return (
      String(x.billingSp ?? '') === String(y.billingSp ?? '') &&
      String(x.prefix ?? '') === String(y.prefix ?? '') &&
      String(x.atmCashRule ?? '') === String(y.atmCashRule ?? '')
    );
  });
