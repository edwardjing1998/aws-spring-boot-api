const handlePrimaryClick = async () => {
  if (mode === 'new') {
    await handleSaveCreate();
  } else if (mode === 'duplicate') {
    await handleDuplicate();
  } else if (mode === 'move') {
    await handleMove();
  } else if (mode === 'changeAll') {
    await handleChangeAll();
  } else {
    alert('Unsupported mode.');
  }
};
