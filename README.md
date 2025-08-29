<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webflux</artifactId>
</dependency>
<dependency>
  <groupId>io.projectreactor.netty</groupId>
  <artifactId>reactor-netty</artifactId>
</dependency>



@Configuration
public class WebClientConfig {
  @Bean
  public WebClient clientWebClient(@Value("${client.base-url:http://localhost:8080}") String baseUrl) {
    return WebClient.builder().baseUrl(baseUrl).build();
  }
}
