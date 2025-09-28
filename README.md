    @GetMapping("client/page")
    public List<ClientDTO> getClientsPaginated(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {

        return clientService.fetchClientsWithPagination(page, size);
    }
