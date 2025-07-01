package rapid.cases.mapper;

import org.mapstruct.InheritConfiguration;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;
import rapid.dto.cases.CaseDTO;

// “live” entities
import rapid.model.cases.address.DltAddress;
import rapid.model.cases.cases.Case;
import rapid.model.cases.accountTransaction.AccountTransaction;
import rapid.model.cases.address.Address;
import rapid.model.cases.bulkCard.BulkCard;
import rapid.model.cases.failedTransaction.DltFailedTransaction;
import rapid.model.cases.failedTransaction.FailedTransaction;
import rapid.model.cases.label.DltLabels;
import rapid.model.cases.label.Labels;
import rapid.model.cases.transaction.Transaction;

// “deleted” entities
import rapid.model.cases.cases.DltCase;
import rapid.model.cases.accountTransaction.DltAccountTransaction;
import rapid.model.cases.bulkCard.DltBulkCard;
import rapid.model.cases.transaction.DltTransaction;

import java.util.List;

/**
 * Maps Case ↔ CaseDTO for both live and deleted variants.
 */
@Mapper(
        componentModel = "spring",
        uses = {
                AccountTransactionMapper.class,
                AddressMapper.class,
                BulkCardMapper.class,
                FailedTransactionMapper.class,
                LabelMapper.class,
                TransactionMapper.class
        }
)
public interface CaseMapper {

    /* ──────────────────────────────────────────────────────────────
       Canonical mapping: Case  →  CaseDTO
       ────────────────────────────────────────────────────────────── */
    @Mappings({
            // 1-1 field mappings
            @Mapping(source = "root.homePhone",              target = "homePhone"),
            @Mapping(source = "root.workPhone",              target = "workPhone"),
            @Mapping(source = "root.entityCode",             target = "entityCd"),
            @Mapping(source = "root.roleCode",               target = "roleCd"),
            @Mapping(source = "root.finalActionCardsNumber", target = "finalActionCardsNr"),
            @Mapping(source = "root.firstUpdateVendorId",    target = "frstUpdtVendId"),
            @Mapping(source = "root.contactCode",            target = "contactCd"),
            @Mapping(source = "root.contactPhoneNumber",     target = "contactPhNr"),
            @Mapping(source = "root.returnReasonCode",       target = "returnReasonCd"),
            @Mapping(source = "root.issuanceCode",           target = "issuanceCd"),
            @Mapping(source = "root.issuanceDate",           target = "issuanceDt"),
            @Mapping(source = "root.workstationName",        target = "workstationNameTx"),
            @Mapping(source = "root.operatorCode",           target = "operatorCd"),
            @Mapping(source = "root.barcodeTypeCode",        target = "barcodeTypeCd"),
            @Mapping(source = "root.recordTypeText",         target = "recTypTx"),
            @Mapping(source = "root.serviceTypeText",        target = "srvcTypTx"),
            @Mapping(source = "root.basicSupplementalId",    target = "bscSpplmntlId"),
            @Mapping(source = "root.originalMailDate",       target = "origMlDt"),
            @Mapping(source = "root.messageId",              target = "msgId"),
            @Mapping(source = "root.mailMethod",             target = "mlMthd"),
            @Mapping(source = "root.customerIdField",        target = "custId"),
            @Mapping(source = "root.msIssueDate",            target = "msIssueDate"),
            @Mapping(source = "root.customerId2",            target = "custId2"),
            @Mapping(source = "root.marketCode",             target = "mktCd"),
            @Mapping(source = "root.reason",                 target = "reason"),
            @Mapping(source = "root.cycle",                  target = "cycle"),
            @Mapping(source = "root.postageBilled",          target = "postageBilled"),
            @Mapping(source = "root.fileSent",               target = "fileSent"),
            @Mapping(source = "root.issuedByAmex",               target = "issuedByAmex"),

            // nested collections
            @Mapping(source = "accountTransactions",  target = "accountTransactions"),
            @Mapping(source = "addresses",  target = "addresses"),
            @Mapping(source = "bulkCards", target = "bulkCards"),
            @Mapping(source = "failedTransactions", target = "failedTransactions"),
            @Mapping(source = "labels",  target = "labels"),
            @Mapping(source = "transactions",  target = "transactions")
    })
    CaseDTO toDto(
            Case root,
            List<AccountTransaction> accountTransactions,
            List<Address>             addresses,
            List<BulkCard>            bulkCards,
            List<FailedTransaction>   failedTransactions,
            List<Labels>              labels,
            List<Transaction>         transactions
    );

    /* ──────────────────────────────────────────────────────────────
       DltCase → CaseDTO
       ────────────────────────────────────────────────────────────── */
    @Mappings({
            // 1-1 field mappings
            @Mapping(source = "root.homePhone",              target = "homePhone"),
            @Mapping(source = "root.workPhone",              target = "workPhone"),
            @Mapping(source = "root.entityCode",             target = "entityCd"),
            @Mapping(source = "root.roleCode",               target = "roleCd"),
            @Mapping(source = "root.finalActionCardsNumber", target = "finalActionCardsNr"),
            @Mapping(source = "root.firstUpdateVendorId",    target = "frstUpdtVendId"),
            @Mapping(source = "root.contactCode",            target = "contactCd"),
            @Mapping(source = "root.contactPhoneNumber",     target = "contactPhNr"),
            @Mapping(source = "root.returnReasonCode",       target = "returnReasonCd"),
            @Mapping(source = "root.issuanceCode",           target = "issuanceCd"),
            @Mapping(source = "root.issuanceDate",           target = "issuanceDt"),
            @Mapping(source = "root.workstationName",        target = "workstationNameTx"),
            @Mapping(source = "root.operatorCode",           target = "operatorCd"),
            @Mapping(source = "root.barcodeTypeCode",        target = "barcodeTypeCd"),
            @Mapping(source = "root.recordTypeText",         target = "recTypTx"),
            @Mapping(source = "root.serviceTypeText",        target = "srvcTypTx"),
            @Mapping(source = "root.basicSupplementalId",    target = "bscSpplmntlId"),
            @Mapping(source = "root.originalMailDate",       target = "origMlDt"),
            @Mapping(source = "root.messageId",              target = "msgId"),
            @Mapping(source = "root.mailMethod",             target = "mlMthd"),
            @Mapping(source = "root.customerIdField",        target = "custId"),
            @Mapping(source = "root.msIssueDate",            target = "msIssueDate"),
            @Mapping(source = "root.customerId2",            target = "custId2"),
            @Mapping(source = "root.marketCode",             target = "mktCd"),
            @Mapping(source = "root.reason",                 target = "reason"),
            @Mapping(source = "root.cycle",                  target = "cycle"),

            // nested collections
            @Mapping(source = "accountTransactions",  target = "accountTransactions"),
            @Mapping(source = "addresses",  target = "addresses"),
            @Mapping(source = "bulkCards", target = "bulkCards"),
            @Mapping(source = "failedTransactions", target = "failedTransactions"),
            @Mapping(source = "labels",  target = "labels"),
            @Mapping(source = "transactions",  target = "transactions")
    })
    CaseDTO toDto(
            DltCase root,
            List<DltAccountTransaction>  accountTransactions,
            List<DltAddress>             addresses,
            List<DltBulkCard>            bulkCards,
            List<DltFailedTransaction>   failedTransactions,
            List<DltLabels>               labels,
            List<DltTransaction>         transactions
    );

    @Mappings({
            @Mapping(source = "homePhone",              target = "homePhone"),
            @Mapping(source = "workPhone",              target = "workPhone"),
            @Mapping(source = "entityCode",             target = "entityCode"),
            @Mapping(source = "roleCode",               target = "roleCode"),
            @Mapping(source = "finalActionCardsNumber", target = "finalActionCardsNumber"),
            @Mapping(source = "firstUpdateVendorId",    target = "firstUpdateVendorId"),
            @Mapping(source = "contactCode",            target = "contactCode"),
            @Mapping(source = "contactPhoneNumber",     target = "contactPhoneNumber"),
            @Mapping(source = "returnReasonCode",       target = "returnReasonCode"),
            @Mapping(source = "issuanceCode",           target = "issuanceCode"),
            @Mapping(source = "issuanceDate",           target = "issuanceDate"),
            @Mapping(source = "workstationName",        target = "workstationName"),
            @Mapping(source = "operatorCode",           target = "operatorCode"),
            @Mapping(source = "barcodeTypeCode",        target = "barcodeTypeCode"),
            @Mapping(source = "recordTypeText",         target = "recordTypeText"),
            @Mapping(source = "serviceTypeText",        target = "serviceTypeText"),
            @Mapping(source = "basicSupplementalId",    target = "basicSupplementalId"),
            @Mapping(source = "originalMailDate",       target = "originalMailDate"),
            @Mapping(source = "messageId",              target = "messageId"),
            @Mapping(source = "mailMethod",             target = "mailMethod"),
            @Mapping(source = "customerIdField",        target = "customerIdField"),
            @Mapping(source = "msIssueDate",            target = "msIssueDate"),
            @Mapping(source = "customerId2",            target = "customerId2"),
            @Mapping(source = "marketCode",             target = "marketCode"),
            @Mapping(source = "reason",                 target = "reason"),
            @Mapping(source = "cycle",                  target = "cycle"),

            @Mapping(source = "accountTransactions", target = "accountTransactions"),
            @Mapping(source = "addresses",           target = "addresses"),
            @Mapping(source = "bulkCards",           target = "bulkCards"),
            @Mapping(source = "failedTransactions",  target = "failedTransactions"),
            @Mapping(source = "labels",              target = "labels"),
            @Mapping(source = "transactions",        target = "transactions"),
    })
    DltCase toDeleted(Case source);

    /* ------- helper -------- */
    default Character nullSafeChar(String s) {
        return (s == null || s.isEmpty()) ? null : s.charAt(0);
    }
}

