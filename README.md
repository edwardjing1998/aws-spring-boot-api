  @GetMapping("/client/page")
  public List<ClientDTO> getClientsPaginated(
      @RequestParam(defaultValue = "0") @Min(0) int page,
      @RequestParam(defaultValue = "10") @Positive int size) {

    return clientService.fetchClientsWithPagination(page, size);
  }
