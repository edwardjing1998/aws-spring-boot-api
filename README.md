    @GetMapping("client/{clientId}")
    public ResponseEntity<List<ClientDTO>> getClientByClientId(@PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String clientId) {

        if (StringUtils.isBlank(clientId)) {
            throw new ClientBadRequestException(
                    "clientId can not be empty");
        }

        List<ClientDTO> clientDTOS = clientService.getAllClientsById(clientId);

        if (clientDTOS.isEmpty()) {
            throw new ClientNotFoundException("Client not found: " + clientId);
        }

        return ResponseEntity.ok(clientDTOS);
    }






        public List<ClientDTO> getAllClientsById(String clientId){

        List<Client> clients = clientRepository.findAllByClient(clientId);

        return clients.stream()
                .map(clientMapper::toDTO)
                .toList();
    }


        @Mappings({
            // BaseClient fields
            @Mapping(source = "client", target = "client"),
            @Mapping(source = "name", target = "name"),
            @Mapping(source = "addr", target = "addr"),
            @Mapping(source = "city", target = "city"),
            @Mapping(source = "state", target = "state"),
            @Mapping(source = "zip", target = "zip"),
            @Mapping(source = "contact", target = "contact"),
            @Mapping(source = "phone", target = "phone"),
            @Mapping(source = "faxNumber", target = "faxNumber"),
            @Mapping(source = "billingSp", target = "billingSp"),
            @Mapping(source = "reportBreakFlag", target = "reportBreakFlag"),
            @Mapping(source = "chLookUpType", target = "chLookUpType"),
            @Mapping(source = "active", target = "active"),
            @Mapping(source = "excludeFromReport", target = "excludeFromReport"),
            @Mapping(source = "positiveReports", target = "positiveReports"),
            @Mapping(source = "subClientInd", target = "subClientInd"),
            @Mapping(source = "subClientXref", target = "subClientXref"),
            @Mapping(source = "amexIssued", target = "amexIssued"),

            // Collection fields
            @Mapping(source = "reportOptions", target = "reportOptions"),
            @Mapping(source = "sysPrinsPrefixes", target = "sysPrinsPrefixes"),
            @Mapping(source = "clientEmails", target = "clientEmail"),
            @Mapping(source = "sysPrins", target = "sysPrins")
    })
    ClientDTO toDTO(Client entity);





    package rapid.dto.client;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Pattern;
import jakarta.validation.constraints.Size;
import lombok.Data;
import rapid.dto.sysPrin.SysPrinDTO;

import java.util.List;

@Data
public class ClientDTO {
    @NotBlank
    @Size(max = 4)
    private String client;
    @Size(max = 30)
    private String name;
    @Size(max = 35)
    private String addr;
    @Size(max = 18)
    private String city;
    @Pattern(regexp = "^[A-Z]{2}$")
    private String state;
    @Size(max = 9)
    private String zip;
    @Size(max = 20)
    private String contact;
    @Size(max = 13)
    private String phone;
    private Boolean active;
    @Size(max = 30)
    private String faxNumber;
    @Size(max = 8)
    private String billingSp;
    private Integer reportBreakFlag;
    private Integer chLookUpType;
    private Boolean excludeFromReport;
    private Boolean positiveReports;
    private Boolean subClientInd;
    @Size(max = 4)
    private String subClientXref;
    private Boolean amexIssued;

    private List<ClientReportOptionDTO> reportOptions;
    private List<SysPrinsPrefixDTO> sysPrinsPrefixes;
    private List<ClientEmailDTO> clientEmail;
    private List<SysPrinDTO> sysPrins;

}


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
