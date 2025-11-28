Argument of type 'ClientRow[]' is not assignable to parameter of type 'ClientGroup[]'.
  Type 'ClientRow' is not assignable to type 'ClientGroup'.
    Types of property 'name' are incompatible.
      Type 'string | undefined' is not assignable to type 'string'.
        Type 'undefined' is not assignable to type 'string'.ts(2345)



     const rows = FlattenClientData(
      clientList,
      selectedClient,
      expandedGroups,
      isWildcardMode,
      sysPrinPageByClient, // ⭐ NEW
      SYS_PRIN_PAGE_SIZE,  // ⭐ NEW
    ) as NavigationRow[];
