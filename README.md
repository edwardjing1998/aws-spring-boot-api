package voltage.service;

import com.fiserv.dataprotector.exception.CryptoException;
import com.fiserv.voltage.FiservProtector;
import lombok.Getter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class FiservProtectorService {

    private final FiservProtector fiservProtector;

    private static final Logger log = LoggerFactory.getLogger(FiservProtectorService.class);

    public FiservProtectorService(@Autowired FiservProtector fiservProtector)
    {
        this.fiservProtector = fiservProtector;
    }

    public String DummyEncrypt() throws CryptoException {
        try {
            String dummpyEncryption = fiservProtector.protect("1111-2222-3333-4444", "Card_Internal");
            // log.info("fiservProtectore :");
            log.info("fiservProtectore successfull :" + dummpyEncryption);
            return dummpyEncryption;
        } catch (CryptoException e) {
            log.error("exception" + e);
            throw new RuntimeException(e);
        }
    }

    private static final String DELIMITER = "==============================================================================";

    public void demo(final String demoName, final String input, final String cryptId, boolean withOutput) {
        if (withOutput) {
            log.info(DELIMITER);
            log.info("Demonstrating operations for '{}', input value '{}' , and cryptid '{}'", demoName, input, cryptId);
        }
        try {
            String encryptedValue = fiservProtector.protect(input, cryptId);
            String decryptedValue = fiservProtector.access(encryptedValue, cryptId);
            if (withOutput) {
                log.info("Value of '{}', after encrypting with the cryptid '{}', is '{}'", input, cryptId, encryptedValue);
                log.info("Value of '{}', after decrypting with the cryptid '{}', is '{}'", encryptedValue, cryptId, decryptedValue);
            }
        } catch (CryptoException e) {
            log.error("Exception performing encryption: {} ", e);
        }
    }
}
