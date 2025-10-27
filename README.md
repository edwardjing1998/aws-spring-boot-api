const pushGeneralPatch = (patch) => {
  const withKey = { sysPrin: selectedData?.sysPrin, ...patch };
  console.log('[General] patch ->', withKey);
  if (typeof onChangeGeneral === 'function') onChangeGeneral(withKey);
  else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKey }));
};
