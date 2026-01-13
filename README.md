useEffect(() => {
  console.log("sysPrin keys:", sysPrin ? Object.keys(sysPrin) : null);
  console.log("raw vendorReceivedFromCount:", sysPrin?.vendorReceivedFromCount);
  console.log("raw vendorForReceivedFromTotal:", sysPrin?.vendorForReceivedFromTotal);
}, [sysPrin]);
