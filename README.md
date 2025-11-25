    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(
        name = "BILLING_SP",
        referencedColumnName = "BILLING_SP",
        insertable = false,
        updatable = false
    )
