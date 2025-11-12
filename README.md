  onClientDeleted={(deletedId) => {
    setRows(prev => prev.filter(r => r.client !== deletedId));
  }}


  onClientDeleted
