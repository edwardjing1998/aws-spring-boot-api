        if (!clients.isEmpty()) {
            log.info("Client ID : {} already exists.", dto.getClient());
            throw new ClientAlreadyExistException(dto.getClient());
        }


{
  "timestamp": "2026-01-20T01:04:16.330+00:00",
  "status": 400,
  "error": "Bad Request",
  "path": "/v1/client/save"
}


package common.exception.client;

import common.exception.EntityAlreadyExistException;
import common.exception.EntityNotFoundException;

public class ClientAlreadyExistException extends EntityAlreadyExistException {
    public ClientAlreadyExistException(String clientId) {
        super("Client", clientId);
    }
}



package common.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(HttpStatus.BAD_REQUEST)
public abstract class EntityAlreadyExistException extends RuntimeException {
    public EntityAlreadyExistException(String entityName, String key) {
        super(entityName + " found for: " + key);
    }
}

