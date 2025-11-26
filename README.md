    @OneToMany
    @JoinColumn(
        name = "SYS_PRIN",         // column in VENDOR_SENT_TO
        referencedColumnName = "SYS_PRIN"  // column in SYS_PRINS
    )
