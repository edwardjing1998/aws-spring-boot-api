package rapid.dto.client;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.validation.constraints.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class ClientReportOptionDTO {

    @JsonProperty("clientId")
    @NotEmpty(message = "clientId is required")
    @Size(max = 15, message = "clientId must not exceed 15 characters")
    private String clientId;

    @JsonProperty("reportId")
    @NotNull(message = "reportId is required")
    @Min(value = 1, message = "reportId must be at least 1")
    private Integer reportId;

    // CHANGED: Boolean -> Integer, and constrained to 0..9
    @JsonProperty("receiveFlag")
    @NotNull(message = "receiveFlag is required")
    @Min(value = 0, message = "receiveFlag must be between 0 and 9")
    @Max(value = 9, message = "receiveFlag must be between 0 and 9")
    private Integer receiveFlag;

    // CHANGED: allow 0..9 (was min 1)
    @JsonProperty("outputTypeCd")
    @NotNull(message = "outputTypeCd is required")
    @Min(value = 0, message = "outputTypeCd must be between 0 and 9")
    @Max(value = 9, message = "outputTypeCd must be between 0 and 9")
    private Integer outputTypeCd;

    // CHANGED: allow 0..9 (was min 1)
    @JsonProperty("fileTypeCd")
    @NotNull(message = "fileTypeCd is required")
    @Min(value = 0, message = "fileTypeCd must be between 0 and 9")
    @Max(value = 9, message = "fileTypeCd must be between 0 and 9")
    private Integer fileTypeCd;

    @JsonProperty("emailFlag")
    @NotNull(message = "emailFlag is required")
    @Min(value = 0, message = "emailFlag must be 0 or 1")
    @Max(value = 1, message = "emailFlag must be 0 or 1")
    private Integer emailFlag;

    @JsonProperty("emailBodyTx")
    @Size(max = 500, message = "emailBodyTx must not exceed 500 characters")
    private String emailBodyTx;

    @JsonProperty("reportPasswordTx")
    @Size(max = 50, message = "reportPasswordTx must not exceed 50 characters")
    private String reportPasswordTx;

    @JsonProperty("reportDetails")
    private AdminQueryListDTO reportDetails;
}
