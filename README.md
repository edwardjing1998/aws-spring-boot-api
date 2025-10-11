DECLARE @page   int = :page;
DECLARE @size   int = :size;
DECLARE @offset int = @page * @size;

WITH page_clients AS (
  SELECT ...
  ORDER BY c.client
  OFFSET @offset ROWS FETCH NEXT @size ROWS ONLY
)
SELECT ( ... ) AS full_json;



var params = new MapSqlParameterSource()
    .addValue("page", page)
    .addValue("size", size);







int offset = page * size;
var params = new MapSqlParameterSource()
    .addValue("offset", offset)
    .addValue("size", size);
return jdbc.queryForObject(fetchFullJsonSql, params, String.class);
