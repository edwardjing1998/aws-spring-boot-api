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

    @JsonProperty("receiveFlag")
    @NotNull(message = "receiveFlag is required")
    private Boolean receiveFlag;

    @JsonProperty("outputTypeCd")
    @NotNull(message = "outputTypeCd is required")
    @Min(value = 1, message = "outputTypeCd must be at least 1")
    private Integer outputTypeCd;

    @JsonProperty("fileTypeCd")
    @NotNull(message = "fileTypeCd is required")
    @Min(value = 1, message = "fileTypeCd must be at least 1")
    private Integer fileTypeCd;

    @JsonProperty("emailFlag")
    @NotNull(message = "emailFlag is required")
    @Min(value = 0, message = "emailFlag must be 0 or 1")
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
