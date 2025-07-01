package rapid.model.cases.cases;

import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.cases.accountTransaction.AccountTransaction;
import rapid.model.cases.address.Address;
import rapid.model.cases.bulkCard.BulkCard;
import rapid.model.cases.failedTransaction.FailedTransaction;
import rapid.model.cases.label.Labels;
import rapid.model.cases.transaction.Transaction;
import rapid.model.cases.base.BaseCase;

import java.util.ArrayList;
import java.util.List;

@Entity
@Data
@Table(name = "CASES")
@NoArgsConstructor
public class Case extends BaseCase {

    @Column(name = "file_sent", length = 1)
    private String fileSent;

    @Column(name = "Issued_by_Amex")
    private Boolean issuedByAmex;

    @Column(name = "Postage_Billed", length = 1)
    private String postageBilled;

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<AccountTransaction> accountTransactions = new ArrayList<>();

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Address> addresses = new ArrayList<>();

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Labels> labels = new ArrayList<>();

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<BulkCard> bulkCards = new ArrayList<>();

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<FailedTransaction> failedTransactions = new ArrayList<>();

    @OneToMany(mappedBy = "caseEntity", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Transaction> transactions = new ArrayList<>();
}
