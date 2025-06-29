public static <T, K> List<T> dedupById(List<T> list, Function<T, K> idExtractor) {
    return new ArrayList<>(list.stream()
        .collect(Collectors.toMap(idExtractor, Function.identity(), (a, b) -> a)) // keep first
        .values());
}


for (DltCase dc : archived) {

    if (dc.getTransactions() != null) {
        dc.setTransactions(
            dedupById(dc.getTransactions(), t -> t.getId())  // assuming getId() returns composite key
        );
        dc.getTransactions().forEach(tx -> tx.setCaseEntity(dc));
    }

    if (dc.getAccountTransactions() != null) {
        dc.setAccountTransactions(
            dedupById(dc.getAccountTransactions(), t -> t.getId())
        );
        dc.getAccountTransactions().forEach(tx -> tx.setCaseEntity(dc));
    }

    if (dc.getAddresses() != null) {
        dc.setAddresses(
            dedupById(dc.getAddresses(), a -> a.getId())
        );
        dc.getAddresses().forEach(addr -> addr.setCaseEntity(dc));
    }

    if (dc.getBulkCards() != null) {
        dc.setBulkCards(
            dedupById(dc.getBulkCards(), b -> b.getId())
        );
        dc.getBulkCards().forEach(b -> b.setCaseEntity(dc));
    }

    if (dc.getFailedTransactions() != null) {
        dc.setFailedTransactions(
            dedupById(dc.getFailedTransactions(), f -> f.getId())
        );
        dc.getFailedTransactions().forEach(f -> f.setCaseEntity(dc));
    }

    if (dc.getLabels() != null) {
        dc.setLabels(
            dedupById(dc.getLabels(), l -> l.getId())
        );
        dc.getLabels().forEach(l -> l.setCaseEntity(dc));
    }
}
