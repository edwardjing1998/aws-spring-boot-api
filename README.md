@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
@JoinColumn(name = "case_number_id", referencedColumnName = "case_number")
