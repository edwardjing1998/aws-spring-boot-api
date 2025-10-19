  @Query("""
         select a.id.area
         from InvalidDelivArea a
         where a.id.sysPrin = :sysPrin
         """)
  Set<String> findAreasBySysPrin(String sysPrin);

  /**
   * Batch insert via saveAll(), creating entities by setting the EmbeddedId.
   * (Matches your BaseInvalidDelivArea model with no all-args constructor.)
   */
  default int bulkInsertAreas(String targetSysPrin, List<String> areas) {
    if (areas == null || areas.isEmpty()) return 0;

    var entities = areas.stream().map(area -> {
      InvalidDelivArea e = new InvalidDelivArea();        // uses @NoArgsConstructor
      e.setId(new InvalidDelivAreaId(targetSysPrin, area)); // set EmbeddedId
      return e;
    }).toList();

    saveAll(entities);
    return entities.size();
  }
