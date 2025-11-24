    @PostMapping("/client-index")
    public ResponseEntity<?> indexClient(@RequestBody ClientSearchDTO dto) {
        try {
            clientService.indexClient(dto.getClient(), dto.getName());
            return ResponseEntity.ok().build();
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Indexing failed: " + e.getMessage());
        }
    }

    @DeleteMapping("/client-index/{clientId}")
    public ResponseEntity<?> deleteClientIndex(@PathVariable String clientId) {
        try {
            clientService.deleteClientFromIndex(clientId);
            return ResponseEntity.ok().build();
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Delete from index failed: " + e.getMessage());
        }
    }
