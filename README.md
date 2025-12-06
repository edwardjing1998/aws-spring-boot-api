    /**
     * Merge rule when duplicate (client|sys_prin) rows exist:
     * 1) Prefer the one with active == true.
     * 2) Otherwise, keep the first encountered (caller uses LinkedHashMap to preserve order).
     *
     * Adjust this rule if you want a different policy (e.g., by updatedAt, holdDays, etc.).
     */
    private SysPrin prefer(SysPrin a, SysPrin b) {
        boolean aActive = Boolean.TRUE.equals(a.getSysPrinActive());
        boolean bActive = Boolean.TRUE.equals(b.getSysPrinActive());
        if (aActive && !bActive) return a;
        if (!aActive && bActive) return b;
        // tie-breaker: keep the first (i.e., 'a')
        return a;
    }
