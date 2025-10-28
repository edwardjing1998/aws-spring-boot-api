- const updateField = (field) => (value) =>
-   setSelectedData((prev) => ({ ...prev, [field]: value }));
+ const updateField = (field) => (value) => pushGeneralPatch({ [field]: value });
