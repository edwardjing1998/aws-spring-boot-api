package admin.repository;

import admin.model.AdminDataDefinitionsList;
import admin.model.AdminQueryList;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface AdminDataDefinitionsRepository extends JpaRepository<AdminDataDefinitionsList, Integer> {

}
