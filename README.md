server:
  port: 8083
  address: 0.0.0.0

spring:
  profiles:
#    active: local
    active: dev

  application:
    name: client-sysprin-reader

swagger:
  server-url: http://localhost:8089/client-sysprin-reader


package rapid.reader;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = {"*"})
@SpringBootApplication(scanBasePackages = {
        "rapid"
})
@EntityScan("rapid.model")
@EnableJpaRepositories("rapid.repository")
public class ClientSysPrinReaderApplication {

    public static void main(String[] args) {
        SpringApplication sa = new SpringApplication(ClientSysPrinReaderApplication.class);
        sa.run(args);
    }
}
