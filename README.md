Error in event handler: ReferenceError: dpaTrace is not defined
    at N (chrome-extension://gofohnjbcabckdcbamaelhmppjoegikd/data/js/Verint.Validator.Content.js:1:1177)Understand this error
:3000/#/edit/client-information-new:1 Error in event handler: ReferenceError: dpaTrace is not defined
    at P (chrome-extension://gofohnjbcabckdcbamaelhmppjoegikd/data/js/Verint.Validator.Content.js:1:1241)Understand this error
chunk-MWBV3TKB.js?v=007909f5:30871 Uncaught Error: error #252 cannot get grid to draw rows when it is in the middle of drawing rows. 
Your code probably called a grid API method while the grid was in the render stage. 
To overcome this, put the API call into a timeout, e.g. instead of api.redrawRows(), call setTimeout(function() { api.redrawRows(); }, 0). 
To see what part of your code that caused the refresh check this stacktrace. 
See https://www.ag-grid.com/react-data-grid/errors/252?_version_=33.2.2
    at RowRenderer.getLockOnRefresh (chunk-MWBV3TKB.js?v=007909f5:30871:13)
    at RowRenderer.redraw (chunk-MWBV3TKB.js?v=007909f5:31124:10)
    at RowRenderer.onCellFocusChanged (chunk-MWBV3TKB.js?v=007909f5:30579:14)
    at cellFocused (chunk-MWBV3TKB.js?v=007909f5:30595:14)
    at callback (chunk-MWBV3TKB.js?v=007909f5:70:124)
    at chunk-MWBV3TKB.js?v=007909f5:74:9
    at Set.forEach (<anonymous>)
    at processEventListeners (chunk-MWBV3TKB.js?v=007909f5:66:82)
    at LocalEventService.dispatchToListeners (chunk-MWBV3TKB.js?v=007909f5:80:7)
    at LocalEventService.dispatchEvent (chunk-MWBV3TKB.js?v=007909f5:50:10)
