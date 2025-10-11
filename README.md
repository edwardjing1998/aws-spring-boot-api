public String fetchClientDetailFullJsonPage(int page, int size) {
  int offset = page * size;
  var params = new MapSqlParameterSource()
      .addValue("offset", offset)
      .addValue("size", size);
  return jdbc.queryForObject(fetchClientDetailsFullJsonSql, params, String.class);
}
