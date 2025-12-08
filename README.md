import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

public interface SysPrinRepository extends JpaRepository<SysPrin, SysPrinId> {

    // Existing simple find
    // Page<SysPrin> findByIdClient(String client, Pageable pageable);

    /**
     * Native Query to deduplicate before pagination.
     * * Logic:
     * 1. Partition by SYS_PRIN (groups duplicates).
     * 2. Order by ACTIVE DESC (puts '1'/'true' on top, matching your 'prefer' logic).
     * 3. Filter where row_num = 1.
     */
    @Query(value = """
        SELECT * FROM (
            SELECT 
                sp.*, 
                ROW_NUMBER() OVER (
                    PARTITION BY sp.sys_prin 
                    ORDER BY sp.ACTIVE DESC, sp.sys_prin ASC
                ) as rn
            FROM SYSTEM_PRINCIPLE sp -- Replace with your actual table name
            WHERE sp.client = :client
        ) deduped
        WHERE deduped.rn = 1
        """, 
        countQuery = """
            SELECT COUNT(DISTINCT sys_prin) 
            FROM SYSTEM_PRINCIPLE 
            WHERE client = :client
        """,
        nativeQuery = true)
    Page<SysPrin> findByIdClientDeduped(@Param("client") String client, Pageable pageable);
}
