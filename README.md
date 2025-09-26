- import { ModuleRegistry } from 'ag-grid-community';
- import { ClientSideRowModelModule } from 'ag-grid-community';
+ import { ModuleRegistry } from 'ag-grid-community';
+ import { ClientSideRowModelModule, InfiniteRowModelModule } from 'ag-grid-community';

...

- ModuleRegistry.registerModules([ClientSideRowModelModule]);
+ ModuleRegistry.registerModules([ClientSideRowModelModule, InfiniteRowModelModule]);
