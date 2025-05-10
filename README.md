package admin.config.data;

import admin.model.*;
import admin.repository.AdminQueryListRepository;
import admin.repository.ClientRepository;
import admin.repository.InvalidDelivAreaRepository;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;

import java.util.*;

@Component
@Profile("local")
public class ClientDataGenerator {

    private final ClientRepository clientRepository;
    private final AdminQueryListRepository adminQueryListRepository;
    private final InvalidDelivAreaRepository invalidDelivAreaRepository;

    private final String[] usStates = {
            "AL", "AK", "AZ", "AR", "CA", "CO", "CT", "DE", "FL", "GA",
            "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MD",
            "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ",
            "NM", "NY", "NC", "ND", "OH", "OK", "OR", "PA", "RI", "SC",
            "SD", "TN", "TX", "UT", "VT", "VA", "WA", "WV", "WI", "WY"
    };

    private final String[] sampleCities = {
            "New York", "Los Angeles", "Chicago", "Houston", "Phoenix", "Philadelphia",
            "San Antonio", "San Diego", "Dallas", "San Jose", "Austin", "Jacksonville",
            "Fort Worth", "Columbus", "Charlotte", "San Francisco", "Indianapolis", "Seattle",
            "Denver", "Washington"
    };

    private final String[] usBanks = {
            "JPMorgan Chase", "Bank of America", "Wells Fargo", "Citigroup", "Goldman Sachs",
            "Morgan Stanley", "U.S. Bancorp", "PNC Financial Services", "Truist Financial", "Capital One Financial",
            "Charles Schwab", "Fifth Third Bancorp", "Ally Financial", "KeyCorp", "Regions Financial",
            "Huntington Bancshares", "First Republic Bank", "M&T Bank Corporation", "Citizens Financial", "Comerica",
            "Zions Bancorporation", "BOK Financial", "Synovus Financial", "Raymond James Financial", "SVB Financial",
            "First Horizon", "East West Bancorp", "New York Community Bancorp", "Wintrust Financial", "Western Alliance"
    };

    private final String[] queryNames = {
            "zipcode step 1 - T", "Phase 1 Retest", "ZipCode Step 2", "Phase3 test",
            "Rapid Daily Activity - AAID", "First Data Rapid Daily Activity Web Report HSBC",
            "Binary - Rapid Daily Activity Report 6056 - NDM", "ProductivityWork_Rpt",
            "Mailing SysPrin - T", "SPS - First Data Rapid Daily Activity Web Report",
            "First Data Rapid Activity Report", "SendToMIT", "Custom Excel Report",
            "Product Group Report", "Account Transfer Rejects Daily Report", "Amex Remail - 1"
    };

    private final Random random = new Random();

    public ClientDataGenerator(ClientRepository clientRepository,
                               AdminQueryListRepository adminQueryListRepository,
                               InvalidDelivAreaRepository invalidDelivAreaRepository) {
        this.clientRepository = clientRepository;
        this.adminQueryListRepository = adminQueryListRepository;
        this.invalidDelivAreaRepository = invalidDelivAreaRepository;
    }

    public void generateClients() {
        List<AdminQueryList> reports = new ArrayList<>();
        for (int j = 1; j <= 10; j++) {
            if (!adminQueryListRepository.existsById(j)) {
                AdminQueryList report = new AdminQueryList();
                report.setDefaultFileNm("Report " + j);
                report.setReportByClientFlag(random.nextBoolean());
                report.setDbDriverType("SQL Server");
                report.setQueryName(queryNames[random.nextInt(queryNames.length)]);
                report.setInputDataFields("Field1, Field2");
                report.setFileExt(random.nextBoolean() ? "TXT" : "XLT");
                report.setFileHeaderInd(1);
                report.setReportDbServer("localhost");
                report.setReportDb("demoDb");
                report.setReportDbUserid("admin");
                report.setReportDbPasswrd("adminpass");
                report.setReportDbIpAndPort("127.0.0.1:1433");
                reports.add(report);
            }
        }

        adminQueryListRepository.saveAll(reports);
        adminQueryListRepository.flush();
        List<AdminQueryList> managedReports = adminQueryListRepository.findAll();

        List<Client> clients = new ArrayList<>();

        for (int i = 0; i < 20; i++) {
            String clientId = generateBase35Id(i);
            String billingSp = "B" + (i + 1);
            String city = sampleCities[random.nextInt(sampleCities.length)];
            String state = usStates[random.nextInt(usStates.length)];
            String zip = String.format("%05d", 10000 + random.nextInt(89999));
            String phone = "555-" + (1000000 + random.nextInt(8999999));
            String name = usBanks[random.nextInt(usBanks.length)];

            List<ClientReportOption> reportOptions = new ArrayList<>();
            for (int j = 1; j <= 10; j++) {
                ClientReportOption option = new ClientReportOption();
                option.setId(new ClientReportOptionId(clientId, j));
                option.setReceiveFlag(j % 2 == 0);
                option.setOutputTypeCd(random.nextInt(3));
                option.setFileTypeCd(random.nextInt(3));
                option.setEmailFlag(random.nextInt(3));
                option.setReportPasswordTx("pass" + j);
                option.setReport(managedReports.get((j - 1) % managedReports.size()));
                option.setEmailBodyTx("Email body for " + clientId + " item " + j);
                reportOptions.add(option);
            }

            Map<String, SysPrinsPrefix> prefixCache = new HashMap<>();
            List<SysPrinsPrefix> prefixes = new ArrayList<>();
            for (int k = 1; k <= 5 + random.nextInt(5); k++) {
                String atmCashRule = random.nextBoolean() ? "0" : "1"; // 1-char
                String prefixStr = "PR" + k; // shortened prefix name to avoid overflow
                String cacheKey = billingSp + "-" + atmCashRule + "-" + prefixStr;

                SysPrinsPrefix existingPrefix = prefixCache.get(cacheKey);
                if (existingPrefix == null) {
                    SysPrinsPrefixId prefixId = new SysPrinsPrefixId(billingSp, prefixStr, atmCashRule);
                    SysPrinsPrefix newPrefix = new SysPrinsPrefix();
                    newPrefix.setId(prefixId);
                    prefixCache.put(cacheKey, newPrefix);
                    prefixes.add(newPrefix);
                } else {
                    prefixes.add(existingPrefix);
                }
            }

            Client client = new Client(
                    clientId, name, "123 " + city + " St.", city, state, zip,
                    "Contact " + (i + 1), phone, (i + 1) % 2 == 0, "Fax-" + (i + 1), billingSp,
                    (i + 1) % 2, (i + 1) % 3, (i + 1) % 2 == 0, (i + 1) % 2 == 1, (i + 1) % 2,
                    "" + generateBase35Id(i + 1), (i + 1) % 2 == 0, reportOptions, prefixes, new ArrayList<>(), new ArrayList<>()
            );

            List<SysPrin> sysPrinsList = new ArrayList<>();
            int sysPrinCount = 2 + random.nextInt(4);
            for (int s = 1; s <= sysPrinCount; s++) {
                SysPrin sysPrin = new SysPrin();
                SysPrinId sysPrinId = new SysPrinId(clientId, "SP" + (s + 1));
                sysPrin.setId(sysPrinId);
                sysPrin.setCustType(String.valueOf(random.nextInt(3)));
                sysPrin.setUndeliverable(String.valueOf(1 + random.nextInt(4)));
                sysPrin.setStatA("1");
                sysPrin.setStatB("0");
                sysPrin.setStatC("1");
                sysPrin.setStatD("1");
                sysPrin.setStatE("0");
                sysPrin.setStatF("1");
                sysPrin.setStatI("1");
                sysPrin.setStatL("0");
                sysPrin.setStatO("1");
                sysPrin.setStatU("1");
                sysPrin.setStatX("0");
                sysPrin.setStatZ("1");
                sysPrin.setPoBox(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setAddrFlag(random.nextBoolean() ? "Y" : "N");
                sysPrin.setTempAway(100L + s);
                sysPrin.setRps(random.nextBoolean() ? "Y" : "N");
                sysPrin.setSession("A");
                sysPrin.setBadState(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setAStatRch(String.valueOf(random.nextInt(2)));
                sysPrin.setNm13(String.valueOf(random.nextInt(2)));
                sysPrin.setTempAwayAtts(200L + s);
                sysPrin.setReportMethod(0.0);
                sysPrin.setActive(true);
                sysPrin.setNotes("Note" + s);
                sysPrin.setReturnStatus("A");
                sysPrin.setDestroyStatus("0");
                sysPrin.setSpecial("1");
                sysPrin.setPinMailer("0");
                sysPrin.setHoldDays(10);
                sysPrin.setNonUS(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setForwardingAddress("1");
                sysPrin.setContact("Contact");
                sysPrin.setPhone("Phone");
                sysPrin.setEntityCode("0");
                sysPrin.setClient(client);

                List<InvalidDelivArea> invalidAreas = new ArrayList<>();
                for (int a = 1; a <= 5; a++) {
                    String randomState = usStates[random.nextInt(usStates.length)];
                    InvalidDelivArea area = new InvalidDelivArea(new InvalidDelivAreaId(sysPrin.getId().getSysPrin(), randomState));
                    invalidAreas.add(area);
                }
                invalidDelivAreaRepository.saveAll(invalidAreas);

                sysPrinsList.add(sysPrin);
            }
            client.setSysPrins(sysPrinsList);

            List<ClientEmail> emails = new ArrayList<>();
            for (int e = 1; e <= 3 + random.nextInt(3); e++) {
                ClientEmail email = new ClientEmail();
                ClientEmailId emailId = new ClientEmailId(clientId, "user" + e + "@example.com");
                email.setId(emailId);
                email.setReportId((long) (1 + random.nextInt(5)));
                email.setEmailNameTx("User" + e);
                email.setCarbonCopyFlag(e % 2 == 0);
                email.setActiveFlag(random.nextBoolean());
                email.setMailServerId((long) random.nextInt(2));
                email.setClient(client);
                emails.add(email);
            }
            client.setClientEmails(emails);

            clients.add(client);
        }

        clientRepository.saveAll(clients);
    }


    private String generateBase35Id(int index) {
        char[] base35Chars = "123456789abcdefghijklmnopqrstuvwxyz".toCharArray();
        StringBuilder sb = new StringBuilder();
        int value = index;
        do {
            sb.insert(0, base35Chars[value % base35Chars.length]);
            value /= base35Chars.length;
        } while (value > 0);
        while (sb.length() < 4) sb.insert(0, '1'); // pad to 4 characters
        return sb.toString();
    }
}
