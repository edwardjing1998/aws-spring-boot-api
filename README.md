@OneToOne
@JoinColumn(
    name = "file_trns_id",
    referencedColumnName = "report_id",
    foreignKey = @ForeignKey(
        name = "fk_adminquerylist_filetransfer",
        foreignKeyDefinition = "FOREIGN KEY (file_trns_id) REFERENCES ADMIN_QUERY_LIST(report_id) ON DELETE CASCADE"
    )
)
private AdminQueryList adminQueryList;



@OneToOne(mappedBy = "adminQueryList", cascade = CascadeType.ALL, orphanRemoval = true)
private C3FileTransfer c3FileTransferParam;


@Transactional
public boolean deleteAdminQueryListAndC3FileTransfer(Integer reportId) {
    Optional<AdminQueryList> optionalAdminQuery = adminQueryListRepository.findById(reportId);

    if (optionalAdminQuery.isPresent()) {
        AdminQueryList adminQuery = optionalAdminQuery.get();

        // Step 1: delete C3FileTransfer manually
        C3FileTransfer relatedTransfer = adminQuery.getC3FileTransferParam();
        if (relatedTransfer != null) {
            c3FileTransferRepository.delete(relatedTransfer);
        }

        // Step 2: delete AdminQueryList
        adminQueryListRepository.delete(adminQuery);

        return true;
    }

    return false;
}

