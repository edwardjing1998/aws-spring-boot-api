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
