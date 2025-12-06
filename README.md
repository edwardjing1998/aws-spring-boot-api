package trace.repository.client;

import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.stereotype.Repository;
import trace.constants.fetchQuery;

@Repository
public class ClientDetailDaoWithNativeSql {

    private final NamedParameterJdbcTemplate jdbc;

    public ClientDetailDaoWithNativeSql(NamedParameterJdbcTemplate jdbc) {
        this.jdbc = jdbc;
    }

    public String fetchClientDetailPageWithNativeSql(int page, int size) {
        var params = new MapSqlParameterSource()
                .addValue("page", page)
                .addValue("size", size);
        String fetchFullJsonSql = fetchQuery.FETCH_CLIENT_DETAIL_FULL_JSON;
        return jdbc.queryForObject(fetchFullJsonSql, params, String.class);
    }
}
