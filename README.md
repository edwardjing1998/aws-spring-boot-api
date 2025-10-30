   const handlePrimaryClick = async () => {
    if (mode === 'new') {
       await handleSaveCreate();
    } else if (mode === 'move') {
      await handleMove();
     } else {
       // You can implement update/other actions here later
       alert('Not in "new" mode. No create action executed.');
     }
   };
