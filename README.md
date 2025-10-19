// src/main/java/rapid/web/sysprin/dto/ChangeAllResponse.java
package rapid.web.sysprin.dto;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class ChangeAllResponse {
  private String clientId;
  private String templateSysPrin;
  private int sysPrinsUpdated;     // how many other sysprins we changed
  private int areasInserted;       // how many invalid_deliv_areas rows we inserted (deduped)
}
