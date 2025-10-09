 @Transactional
    public String deleteClientDeep(String clientId) {
        Client client = clientRepository.findById(clientId)
                .orElseThrow(() -> new IllegalArgumentException("Client not found: " + clientId));

        // 1) bulk-delete children (no hydration => no duplicate-id errors)
        emailRepository.hardDeleteByClient(clientId);
        reportRepository.hardDeleteByClient(clientId);
        sysPrinRepository.hardDeleteByClient(clientId);
        if (client.getBillingSp() != null) {
            sysPrinsPrefixRepository.hardDeleteByBillingSp(client.getBillingSp());
        }

        // 2) delete the parent
        clientRepository.hardDeleteByClient(clientId);

       return "Client details deleted successfully for ID: " + clientId;
    }
