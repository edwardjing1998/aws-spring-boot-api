package voltage.service;

import com.fiserv.dataprotector.exception.CryptoException;
import com.fiserv.voltage.FiservProtector;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

@Slf4j
@Component
@RequiredArgsConstructor
public class FiservProtectorService {

    private final FiservProtector fiservProtector;

    private static final String DEFAULT_CRYPT_ID = "Card_Internal";
    private static final String DELIMITER =
            "==============================================================================";

    /** Simple test to verify wiring/round-trip encryption. */
    public String dummyEncrypt() throws CryptoException {
        String encrypted = fiservProtector.protect("1111-2222-3333-4444", DEFAULT_CRYPT_ID);
        log.info("fiservProtector dummyEncrypt succeeded: {}", encrypted);
        return encrypted;
    }

    /** Convenience helpers if you need them elsewhere. */
    public String encrypt(String plaintext, String cryptId) throws CryptoException {
        return fiservProtector.protect(plaintext, cryptId);
    }

    public String decrypt(String ciphertext, String cryptId) throws CryptoException {
        return fiservProtector.access(ciphertext, cryptId);
    }

    /** Demo with optional logging. */
    public void demo(final String demoName, final String input, final String cryptId, boolean withOutput) {
        if (withOutput) {
            log.info(DELIMITER);
            log.info("Demonstrating '{}' | input='{}' | cryptId='{}'", demoName, input, cryptId);
        }
        try {
            String encrypted = fiservProtector.protect(input, cryptId);
            String decrypted = fiservProtector.access(encrypted, cryptId);
            if (withOutput) {
                log.info("Encrypted: {}", encrypted);
                log.info("Decrypted: {}", decrypted);
            }
        } catch (CryptoException e) {
            log.error("Exception during demo (cryptId={}): {}", cryptId, e.getMessage(), e);
        }
    }
}
