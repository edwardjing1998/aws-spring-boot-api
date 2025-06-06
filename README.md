package admin.config.data;

import admin.model.*;
import admin.repository.*;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.List;
import java.util.Random;

@Component
@Profile("local")
public class CaseDataGenerator {

    private final CaseRepository caseRepository;
    private final ClientRepository clientRepository;
    private final AddressRepository addressRepository;
    private final AccountTransactionRepository accountTransactionRepository;
    private final BulkCardRepository bulkCardRepository;
    private final FailedTransactionRepository failedTransactionRepository;
    private final LabelsRepository labelsRepository;

    private final Random random = new Random();
    private final Character[] dispositions = {'R', 'H', 'D', 'B', 'S'};

    private final String[][] sampleAddresses = {
            {"123 Elm St", "New York", "NY"},
            {"456 Oak Ave", "Los Angeles", "CA"},
            {"789 Maple Dr", "Chicago", "IL"},
            {"321 Pine Rd", "Houston", "TX"},
            {"654 Cedar Blvd", "Phoenix", "AZ"},
            {"987 Birch Ln", "Philadelphia", "PA"},
            {"159 Spruce Ct", "San Antonio", "TX"},
            {"753 Walnut Way", "San Diego", "CA"},
            {"852 Chestnut Pl", "Dallas", "TX"},
            {"963 Poplar Cir", "San Jose", "CA"}
    };

    public CaseDataGenerator(
            CaseRepository caseRepository,
            ClientRepository clientRepository,
            AddressRepository addressRepository,
            AccountTransactionRepository accountTransactionRepository,
            BulkCardRepository bulkCardRepository,
            FailedTransactionRepository failedTransactionRepository,
            LabelsRepository labelsRepository
    ) {
        this.caseRepository = caseRepository;
        this.clientRepository = clientRepository;
        this.addressRepository = addressRepository;
        this.accountTransactionRepository = accountTransactionRepository;
        this.bulkCardRepository = bulkCardRepository;
        this.failedTransactionRepository = failedTransactionRepository;
        this.labelsRepository = labelsRepository;
    }

    public void generateCases() {
        List<Client> clients = clientRepository.findAllWithSysPrins();
        if (clients.isEmpty()) return;

        for (Client client : clients) {
            List<SysPrin> sysPrins = client.getSysPrins();
            if (sysPrins == null || sysPrins.isEmpty()) continue;

            // For each client, create 5 distinct Case rows
            for (int i = 1; i <= 5; i++) {
                int dispositionIndex = i % dispositions.length;
                Character disposition = dispositions[dispositionIndex];

                // Pick one of this client’s SysPrin codes
                SysPrin sysPrin = sysPrins.get(dispositionIndex % sysPrins.size());
                String sysPrinCode = sysPrin.getId().getSysPrin();

                // -------------- 1. Build the new Case --------------
                Case c = new Case();
                String caseNum = client.getClient() + disposition + "C" + i;
                c.setCaseNumber(caseNum);
                c.setPiId("PI" + i);
                c.setAccount("ACC" + random.nextInt(10000));
                c.setLastName("Doe" + i);
                c.setFirstName("John" + i);
                c.setStatus(new char[]{'A','X','D','Z'}[random.nextInt(4)]);
                c.setNumCards(random.nextInt(5) + 1);
                c.setNextDate(LocalDateTime.now().plusDays(i));
                c.setReason(new char[]{'0','1','2','3','4','5'}[random.nextInt(6)]);
                c.setDisposition(disposition);
                c.setInDate(LocalDateTime.now());
                c.setDeliveryId(i);
                c.setSysPrin(sysPrinCode);
                c.setOutDate(LocalDateTime.now().plusDays(5));
                c.setCycle(new char[]{'C','0'}[random.nextInt(2)]);
                c.setActive(random.nextBoolean());
                c.setInHour(1);
                c.setAutoDate(LocalDateTime.now().plusDays(2));
                c.setSubreason(i);
                c.setHmPhone("416-hm-" + i);
                c.setWkPhone("416-wk-" + i);

                // -------------- 2. Build and attach the Address --------------
                Address address = new Address();
                address.setCaseNumberId(caseNum);
                address.setAddressEntityCd("1");
                String[] a = sampleAddresses[random.nextInt(sampleAddresses.length)];
                address.setAddr1(a[0]);
                address.setCity(a[1]);
                address.setState(a[2]);
                address.setZip("1000" + i);
                address.setAddr2("Suite " + i);
                address.setAddr3("Building " + i);
                address.setAddr4("Floor " + i);
                address.setAlternateName("AltName" + i);
                address.setDeliveryPointCd(String.valueOf(i));
                // Persist Address by itself since Address isn’t cascaded from Case
                addressRepository.save(address);

                // -------------- 3. Build and attach AccountTransaction children --------------
                // Each Case has 1 AccountTransaction in this example
                AccountTransactionId txId = new AccountTransactionId(
                        caseNum,
                        BigDecimal.valueOf(i),
                        LocalDateTime.now().minusDays(i)
                );
                AccountTransaction tx = new AccountTransaction();
                tx.setId(txId);
                tx.setCaseEntity(c);
                tx.setPiId(c.getPiId());
                tx.setAccount(c.getAccount());
                tx.setActionId("ROA");
                tx.setUid("UID" + i);
                tx.setDocumentNo("DOC" + i);
                tx.setSysPrin(sysPrinCode);
                tx.setNoCards(random.nextInt(5) + 1);
                tx.setActionReason("Test reason " + i);
                tx.setOperatorTime(random.nextInt(500));
                tx.setWorkstationName("WS" + i);
                tx.setPostageCategoryCd((short)(i % 4));
                tx.setAltAcctId("ALT" + i);
                tx.setMemberSeqId("SEQ" + i);
                c.getAccountTransactions().add(tx);

                // -------------- 4. Build and attach 2 BulkCard children --------------
                for (int bc = 1; bc <= 2; bc++) {
                    String bulkPiId = "BULKPI" + (i * 10 + bc);
                    LocalDateTime inDate = LocalDateTime.now().minusHours(bc);
                    BulkCardId bulkCardId = new BulkCardId(caseNum, c.getPiId(), bulkPiId, inDate);
                    BulkCard bcEntity = new BulkCard();
                    bcEntity.setId(bulkCardId);
                    bcEntity.setCaseEntity(c);
                    c.getBulkCards().add(bcEntity);
                }

                // -------------- 5. Build and attach 2 FailedTransaction children --------------
                for (int ftc = 1; ftc <= 2; ftc++) {
                    // Assume your FailedTransactionId is something like (caseNumber, transNo, dateTime)?
                    // Adjust arguments to match your actual Embeddable key.
                    FailedTransactionId ftId = new FailedTransactionId(caseNum, BigDecimal.valueOf(ftc));
                    FailedTransaction ft = new FailedTransaction();
                    ft.setId(ftId);
                    ft.setCaseEntity(c);
                    ft.setCommandLine("commandLine " + ftc);
                    ft.setCycle(String.valueOf(ftc));
                    ft.setDateTime(LocalDateTime.now().minusHours(ftc));
                    ft.setType((short)(i % 4));
                    ft.setRetryCount((short)(i % 4));
                    ft.setSystemType("sys" + i);
                    c.getFailedTransactions().add(ft);
                }

                for (int l = 1; l <= 5; l++) {
                    Labels lbl = new Labels();
                    LabelsId lblId = new LabelsId();

                    lblId.setText1("TEXT1_SAMPLE_" + i + "_" + l);
                    lblId.setText2("TEXT2_SAMPLE_" + i + "_" + l);
                    lblId.setText3("TEXT3_SAMPLE_" + i + "_" + l);
                    lblId.setText4("TEXT4_SAMPLE_" + i + "_" + l);
                    lblId.setText5("TEXT5_SAMPLE_" + i + "_" + l);
                    lblId.setStatus(i % 2 == 0);
                    lblId.setType("PRINT");

                    // Incorporate `l` into at least one of these fields too
                    lblId.setCardTypeTx("CT" + i + "_" + l);
                    lblId.setBarCodeTx("BARCODE_" + String.format("%05d", i*10 + l));

                    lblId.setText3Addr3("ADDR3_SAMPLE_" + i + "_" + l);
                    lblId.setText3Addr4("ADDR4_SAMPLE_" + i + "_" + l);

                    lblId.setCaseNumber(caseNum);
                    lbl.setId(lblId);
                    lbl.setCaseEntity(c);
                    c.getLabels().add(lbl);
                }

                caseRepository.save(c);
            }
        }
    }
}
