    @PostMapping("email/add")
    public ResponseEntity<?> addClientEmail(@RequestBody ClientEmailDTO emailDTO) {

        log.info("the client id {} requested for add email ", emailDTO.getClientId());

        if (!CLINT_ID_PATTERN.matcher(emailDTO.getClientId().trim()).matches()) {
            log.info("the client id added {} is invalid ", emailDTO.getClientId());
            throw new ClientBadRequestException("Invalid 'clientNumber': must be exactly 4 digits and can not contains alphabetic characters");
        }

        String email = emailDTO.getEmailAddressTx();
        
        if (email == null || !EMAIL_PATTERN.matcher(email.trim()).matches()) {
            throw new ClientBadRequestException("Invalid email address to add: " + email);
        }
        

        return clientEmailService.saveClientEmailFromDTO(emailDTO)
                .<ResponseEntity<?>>map(ResponseEntity::ok)
                .orElseThrow(() -> new ClientNotFoundException("Client not found: " + emailDTO.getClientId()));
    }
