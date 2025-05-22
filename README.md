Uncaught Error: error #252 cannot get grid to draw rows when it is in the middle of drawing rows. 
Your code probably called a grid API method while the grid was in the render stage. 
To overcome this, put the API call into a timeout, e.g. instead of api.redrawRows(), call setTimeout(function() { api.redrawRows(); }, 0). 
To see what part of your code that caused the refresh check this stacktrace. 
See https://www.ag-grid.com/react-data-grid/errors/252?_version_=33.2.2
    at RowRenderer.getLockOnRefresh (main.esm.mjs:31151:13)
    at RowRenderer.redraw (main.esm.mjs:31402:10)
    at RowRenderer.onCellFocusChanged (main.esm.mjs:30864:14)
    at cellFocused (main.esm.mjs:30880:14)
    at callback (main.esm.mjs:71:124)
    at main.esm.mjs:75:9
    at Set.forEach (<anonymous>)
    at processEventListeners (main.esm.mjs:67:82)
    at LocalEventService.dispatchToListeners (main.esm.mjs:81:7)
    at LocalEventService.dispatchEvent (main.esm.mjs:51:10)
