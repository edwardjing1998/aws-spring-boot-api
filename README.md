// src/main/java/voltage/config/ProtectorConfig.java
package voltage.config;

import com.fiserv.voltage.FiservProtector;
import com.fiserv.voltage.DataProtectorAdapter;
import com.fiserv.dataprotector.DataProtector;
import com.fiserv.dataprotector.exception.CryptoException;
import org.springframework.context.annotation.*;

import java.util.List;

@Configuration
public class ProtectorConfig {

  /** ---- REAL BEAN (used in all profiles except 'dev') ---- */
  @Bean
  @Profile("!dev")
  public FiservProtector fiservProtector() {
    // TODO: build/configure the real DataProtector from your env/properties.
    // The exact builder/ctor depends on your library. Example placeholder:
    DataProtector dp = DataProtector.builder()
        // .withKeyServerUrl(...)
        // .withClientId(...)
        // .withClientSecret(...)
        // .withPolicyName(...)
        .build();

    // MaskApplier: use the default/appropriate implementation from your library.
    MaskApplier maskApplier = new MaskApplier(); // replace if different

    // cryptId your service uses by default (matches your current code)
    String supportedCryptId = "Card_Internal";
    return new DataProtectorAdapter(supportedCryptId, dp, maskApplier);
  }

  /** ---- DEV STUB (so `spring.profiles.active=dev` runs without external deps) ---- */
  @Bean
  @Primary
  @Profile("dev")
  public FiservProtector fiservProtectorDevStub() {
    return new FiservProtector() {
      private static final String ENC_PREFIX = "enc(";
      private static final String ENC_SUFFIX = ")";

      @Override public String protect(String v, String id) { return v == null ? null : ENC_PREFIX + v + "|" + id + ENC_SUFFIX; }
      @Override public String access(String v, String id)  {
        if (v == null) return null;
        int bar = v.indexOf('|');
        if (v.startsWith(ENC_PREFIX) && v.endsWith(ENC_SUFFIX) && bar > 0) {
          return v.substring(ENC_PREFIX.length(), v.length() - ENC_SUFFIX.length()).split("\\|",2)[0];
        }
        return v; // passthrough
      }
      // Minimal pass-throughs for other overloads so the app can boot in dev:
      @Override public char[] protect(char[] v, String id) { return v; }
      @Override public List<String> protect(List<String> v, String id) { return v; }
      @Override public char[][] protect(char[][] v, String id) { return v; }
      @Override public byte[] protect(byte[] v, String id) { return v; }
      @Override public char[] access(char[] v, String id) { return v; }
      @Override public List<String> access(List<String> v, String id) { return v; }
      @Override public char[][] access(char[][] v, String id) { return v; }
      @Override public byte[] access(byte[] v, String id) { return v; }
      @Override public String accessMasked(String v, String id) { return v; }
      @Override public char[] accessMasked(char[] v, String id) { return v; }
      @Override public List<String> accessMasked(List<String> v, String id) { return v; }
      @Override public char[][] accessMasked(char[][] v, String id) { return v; }
      @Override public void cleanup() {}
    };
  }
}
