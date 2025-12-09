package rapid.model.client.base;

import jakarta.persistence.Column;
import jakarta.persistence.EmbeddedId;
import jakarta.persistence.MappedSuperclass;
import lombok.Data;
import rapid.model.client.key.ClientReportOptionId;

@MappedSuperclass
@Data
public class BaseClientReportOption {

    @EmbeddedId
    private ClientReportOptionId id = new ClientReportOptionId();

    @Column(name = "RECEIVE_FLAG", nullable = false)
    private Boolean receiveFlag;

    @Column(name = "OUTPUT_TYPE_CD", nullable = false)
    private Integer outputTypeCd;

    @Column(name = "FILE_TYPE_CD", nullable = false)
    private Integer fileTypeCd;

    @Column(name = "EMAIL_FLAG", nullable = false)
    private Integer emailFlag;

    @Column(name = "EMAIL_BODY_TX")
    private String emailBodyTx;

    @Column(name = "REPORT_PASSWORD_TX")
    private String reportPasswordTx;
}

package rapid.model.client.key;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;
import java.util.Objects;

@Embeddable
@Data
@AllArgsConstructor
@NoArgsConstructor
public class ClientReportOptionId implements Serializable {

    @Column(name = "client_id", length = 15)
    private String clientId;

    @Column(name = "report_id")
    private Integer reportId;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof ClientReportOptionId)) return false;
        ClientReportOptionId that = (ClientReportOptionId) o;
        return Objects.equals(clientId, that.clientId) &&
                Objects.equals(reportId, that.reportId);
    }

    @Override
    public int hashCode() {
        return Objects.hash(clientId, reportId);
    }
}
