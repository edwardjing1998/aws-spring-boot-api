public record ClientDTO(
  @NotBlank @Size(max = 60) String name,
  @Size(max = 120) String addr,
  @Size(max = 40) String city,
  @Pattern(regexp = "^[A-Z]{2}$") String state,
  @Size(max = 10) String zip
) {}



public ResponseEntity<ClientDTO> update(@Valid @RequestBody ClientDTO dto) {
