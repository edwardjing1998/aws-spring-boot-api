    public List<Client> clientIsExist(String clientId){
        return clientRepository.findAllByClient(clientId);
    }
