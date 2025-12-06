package trace.repository.client;

import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.stereotype.Repository;
import trace.config.SqlQueryConfig; // 1. Import your config class

@Repository
public class ClientDetailDaoWithNativeSql {

    private final NamedParameterJdbcTemplate jdbc;
    
    // 2. Declare a final field for the config
    private final SqlQueryConfig sqlConfig; 

    // 3. Update constructor to accept the config
    // Spring automatically injects beans found in the constructor arguments
    public ClientDetailDaoWithNativeSql(NamedParameterJdbcTemplate jdbc, 
                                        SqlQueryConfig sqlConfig) {
        this.jdbc = jdbc;
        this.sqlConfig = sqlConfig;
    }

    public String fetchClientDetailPageWithNativeSql(int page, int size) {
        var params = new MapSqlParameterSource()
                .addValue("page", page)
                .addValue("size", size);
        
        // 4. Retrieve the SQL from the injected object
        // (Assuming your structure in SqlQueryConfig is sql -> client -> fetchClientDetailFullJson)
        String fetchFullJsonSql = sqlConfig.getClient().getFetchClientDetailFullJson();
        
        // Optional: Fail-fast check to catch YAML typos early
        if (fetchFullJsonSql == null || fetchFullJsonSql.isBlank()) {
            throw new IllegalStateException("SQL query 'fetchClientDetailFullJson' not found in YAML config");
        }

        return jdbc.queryForObject(fetchFullJsonSql, params, String.class);
    }
}
