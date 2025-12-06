import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Data
@Configuration
@PropertySource(value = "classpath:sql-queries.yml", factory = YamlPropertySourceFactory.class)
@ConfigurationProperties(prefix = "sql")
public class SqlQueryConfig {
    
    private UserQueries users;

    @Data
    public static class UserQueries {
        private String findActiveWithOrders;
        private String updateStatusBulk;
    }
}
