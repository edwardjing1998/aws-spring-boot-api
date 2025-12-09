package rapid.repository.client;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import rapid.model.client.AdminQueryList;

import java.util.List;

@Repository
public interface AdminQueryListRepository extends JpaRepository<AdminQueryList, Integer> {
    @Query("SELECT a.reportId FROM AdminQueryList a")
    List<Integer> findAllReportIds();

    @Query("SELECT a FROM AdminQueryList a  where a.reportId = :reportId")
    List<AdminQueryList> findByReportId(@Param("reportId") Integer reportId);
}
