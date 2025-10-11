Set<String> ids = clients.getContent().stream()
    .map(Client::getClient)
    .collect(Collectors.toSet());
