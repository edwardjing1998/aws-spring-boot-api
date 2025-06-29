for (DltCase dc : archived) {

    // 🧹 Deduplication logic helper
    <T, K> List<T> dedup(List<T> list, Function<T, K> keyExtractor) {
        return new ArrayList<>(list.stream()
            .collect(Collectors.toMap(keyExtractor, Function.identity(), (a, b) -> a))
            .values());
    }

    if (dc.getAccountTransactions() != null) {
        dc.setAccountTransactions(
            dedup(dc.getAccountTransactions(), atx -> atx.getId())
        );
        dc.getAccountTransactions().forEach(atx -> atx.setCaseEntity(dc));
        log.info("getAccountTransactions size {} ", dc.getAccountTransactions().size());
    }

    if (dc.getAddresses() != null) {
        dc.setAddresses(
            dedup(dc.getAddresses(), a -> a.getId())
        );
        dc.getAddresses().forEach(addr -> addr.setCaseEntity(dc));
        log.info("getAddresses size {} ", dc.getAddresses().size());
    }

    if (dc.getBulkCards() != null) {
        dc.setBulkCards(
            dedup(dc.getBulkCards(), b -> b.getId())
        );
        dc.getBulkCards().forEach(bc -> bc.setCaseEntity(dc));
        log.info("getBulkCards size {} ", dc.getBulkCards().size());
    }

    if (dc.getFailedTransactions() != null) {
        dc.setFailedTransactions(
            dedup(dc.getFailedTransactions(), f -> f.getId())
        );
        dc.getFailedTransactions().forEach(ft -> ft.setCaseEntity(dc));
        log.info("getFailedTransactions size {} ", dc.getFailedTransactions().size());
    }

    if (dc.getLabels() != null) {
        dc.setLabels(
            dedup(dc.getLabels(), l -> l.getId())
        );
        dc.getLabels().forEach(lb -> lb.setCaseEntity(dc));
        log.info("getLabels size {} ", dc.getLabels().size());
    }

    if (dc.getTransactions() != null) {
        dc.setTransactions(
            dedup(dc.getTransactions(), t -> t.getId())
        );
        dc.getTransactions().forEach(tx -> tx.setCaseEntity(dc));
        log.info("getTransactions size {} ", dc.getTransactions().size());
    }
}
