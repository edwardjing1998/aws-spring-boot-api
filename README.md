package rapid.model.client;

import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.client.base.BaseClient;
import rapid.model.sysprin.SysPrin;

import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "CLIENTS")
@Data
@NoArgsConstructor
public class Client extends BaseClient {

    @OneToMany(mappedBy = "id.clientId", cascade = CascadeType.ALL)
    private List<ClientReportOption> reportOptions = new ArrayList<>();

    @OneToMany(mappedBy = "id.billingSp", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<SysPrinsPrefix> sysPrinsPrefixes = new ArrayList<>();

    @OneToMany(mappedBy = "id.client", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<SysPrin> sysPrins = new ArrayList<>();

    @OneToMany(mappedBy = "id.clientId", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ClientEmail> clientEmails = new ArrayList<>();
}
