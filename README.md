Page<ClientReportOption> findByIdClientId(String clientId, Pageable pageable);


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
