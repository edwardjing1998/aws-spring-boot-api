// Collect area/vendor slices that should also be copied on "Change All"
const getSourceVendorPatch = () => {
  const sd = selectedData ?? {};
  return {
    vendorReceivedFrom: Array.isArray(sd.vendorReceivedFrom) ? sd.vendorReceivedFrom : [],
    vendorSentTo: Array.isArray(sd.vendorSentTo) ? sd.vendorSentTo : [],
    invalidDelivAreas: Array.isArray(sd.invalidDelivAreas) ? sd.invalidDelivAreas : [],
    // notes is optional – include if you want notes copied to all as well
    notes: sd.notes ?? '',
  };
};
