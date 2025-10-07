package rapid.searchintegration;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.FilterType;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = {"*"})
@SpringBootApplication
@EntityScan("rapid.model")
@EnableJpaRepositories(
        basePackages = "rapid",   excludeFilters = {
        // Exclude by concrete type(s)
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {
                rapid.repository.client.ClientDetailDaoWithNativeSql.class,
                rapid.repository.sysprin.SysPrinDetailDaoWithNativeSql.class
        }),
})
public class SearchIntegrationApplication {

    public static void main(String[] args) {
        SpringApplication.run(SearchIntegrationApplication.class, args);
    }

}









package rapid.writer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.FilterType;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = { "*" })
@SpringBootApplication
@EntityScan("rapid.model")
@EnableJpaRepositories(
        basePackages = "rapid",
        excludeFilters = {
        // Exclude by concrete type(s)
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {
                rapid.repository.client.ClientDetailDaoWithNativeSql.class,
                rapid.repository.sysprin.SysPrinDetailDaoWithNativeSql.class
        }),
})
public class ClientSysPrinWriterApplication {

    public static void main(String[] args) {
        SpringApplication sa = new SpringApplication(ClientSysPrinWriterApplication.class);
        sa.run(args);
    }




    No operations defined in spec!
}
