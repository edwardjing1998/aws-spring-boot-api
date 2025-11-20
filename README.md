server:
  port: 8089

spring:
  application:
    name: gateway
  http:
    client:
      connect-timeout: 2000

  cloud:
    gateway:
      mvc:
        default-filters:
          - Retry=3
        global-cors:
          corsConfigurations:
            '[/**]':
              allowedOrigins: "*"
              allowedMethods: "*"
              allowedHeaders: "*"
              allowCredentials: true
        httpclient:
          response-timeout: 5s

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,loggers,gateway,mappings

logging:
  level:
    org.springframework.cloud.gateway: INFO
    reactor.netty.http.client: WARN

springdoc:
  swagger-ui:
    urls:
      - name: client-sysprin-reader
        url: "http://localhost:8089/client-sysprin-reader/v3/api-docs"
      - name: client-sysprin-writer
        url: "http://localhost:8089/client-sysprin-writer/v3/api-docs"
      - name: search-integration
        url: "http://localhost:8089/search-integration/v3/api-docs"
