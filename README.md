/**
     * Merge rule when duplicate (client|sys_prin) rows exist:
     * 1) Prefer the one with active == "1".
     * 2) Otherwise, keep the first encountered.
     */
    private SysPrin prefer(SysPrin a, SysPrin b) {
        // UPDATED: Check for String "1" instead of Boolean object
        boolean aActive = "1".equals(a.getSysPrinActive());
        boolean bActive = "1".equals(b.getSysPrinActive());

        if (aActive && !bActive) return a;
        if (!aActive && bActive) return b;
        
        // tie-breaker: keep the first (i.e., 'a')
        return a;
    }
