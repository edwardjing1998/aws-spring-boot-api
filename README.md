// add this near other callbacks
const handleClientDeleted = React.useCallback((deletedId) => {
if (!deletedId) return;
setClientList(prev => prev.filter(c => String(c.client) !== String(deletedId)));
setSelectedGroupRow(prev => (prev && String(prev.client) === String(deletedId)) ? null : prev);
setSelectedData(prev => (prev && String(prev.client) === String(deletedId)) ? defaultSelectedData : prev);
}, []);


onClientDeleted={handleClientDeleted}
