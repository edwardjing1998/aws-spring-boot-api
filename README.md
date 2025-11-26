    @OneToMany(mappedBy = "sysPrin", cascade = CascadeType.ALL, orphanRemoval = false)
    private List<VendorReceivedFrom> vendorSentTo = new ArrayList<>();


    // JOIN to SYS_PRINS on SYS_PRIN column (part of SysPrin PK)
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(
        name = "SYS_PRIN",              // column in VENDOR_SENT_TO table
        referencedColumnName = "SYS_PRIN",
        insertable = false,
        updatable = false
    )
    private SysPrin sysPrin;
