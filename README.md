package admin.repository;

import admin.model.AdminQueryList;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

@Repository
public interface AdminQueryListRepository extends JpaRepository<AdminQueryList, Integer> {
    List<AdminQueryList> findByReportByClientFlagTrue();
    List<AdminQueryList> findByReportByClientFlagFalse();

    List<AdminQueryList> findAllByFileTransferType(Integer fileTransferType);
}
