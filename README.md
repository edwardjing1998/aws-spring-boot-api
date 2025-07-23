package admin.repository;

import admin.dto.AdminQueryToolTipsDTO;
import admin.model.AdminQueryToolTips;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface AdminQueryToolTipsRepository extends JpaRepository<AdminQueryToolTips, Integer> {

    List<AdminQueryToolTips> findAllByReportId(Integer reportId);
}




package admin.repository;

import admin.model.AdminTableLoadColumnMapping;
import admin.model.ReportIdforColumnMapping;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;


@Repository
public interface AdminTableLoadColumnMappingRepository extends JpaRepository<AdminTableLoadColumnMapping, ReportIdforColumnMapping> {

    List<AdminTableLoadColumnMapping>  findByReportIdReportId(Integer reportId);

    Integer deleteByReportId(ReportIdforColumnMapping reportId);
}
