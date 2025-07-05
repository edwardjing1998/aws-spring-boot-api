package rapid.client.web;

import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.client.service.ClientService;
import rapid.dto.client.ClientDTO;

import java.util.List;

@RestController
@RequestMapping("/api/clients")
@RequiredArgsConstructor
public class ClientController {

    private final ClientService clientService;

    @GetMapping("all")
    public ResponseEntity<List<ClientDTO>> getAllClients() {
        List<ClientDTO> clients = clientService.getAllClients();
        return ResponseEntity.ok(clients);
    }
}







package rapid.client.web;

import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.client.service.ClientService;
import rapid.dto.client.ClientDTO;

import java.util.List;

@RestController
@RequestMapping("/api/clients")
@RequiredArgsConstructor
public class ClientController {

    private final ClientService clientService;

    @GetMapping("all")
    public ResponseEntity<List<ClientDTO>> getAllClients() {
        List<ClientDTO> clients = clientService.getAllClients();
        return ResponseEntity.ok(clients);
    }
}





spring:
  application:
    name: client-sysprin-reader       # ← nested style

  datasource:
    url: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE;MODE=MYSQL
    driver-class-name: org.h2.Driver
    username: sa
    password:

  jpa:
    show-sql: true
    hibernate:
      ddl-auto: none

  h2:
    console:
      enabled: true
      path: /h2-console
      settings:
        web-allow-others: true      # enables console from outside container

  liquibase:
    change-log: classpath:db/changelog/db.changelog-master.xml

logging:
  level:
    org.springframework.web: DEBUG

server:
  port: 8083
  address: 0.0.0.0

swagger:
  server-url: http://localhost:8083/client-sysprin-reader

