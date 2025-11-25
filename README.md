    @ManyToOne(fetch = FetchType.LAZY)
    @MapsId("billingSp")               // 👈 now valid: maps to Client’s @Id
    @JoinColumn(name = "BILLING_SP")
    private Client client;

    @OneToMany(mappedBy = "client", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<SysPrinsPrefix> sysPrinsPrefixes = new ArrayList<>();
