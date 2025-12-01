private String toKey(SysPrin sp) {
    return sp.getId().getClient() + "|" + sp.getId().getSysPrin();
}

/**
 * Decide which SysPrin to keep when duplicates share the same key.
 * For now, keep the first; you can change to use updatedAt, active, etc.
 */
private SysPrin prefer(SysPrin existing, SysPrin incoming) {
    // TODO: if you have timestamp or "active" flag, pick the better one here.
    return existing; // keep the first
}
