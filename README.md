package admin.repository;

import admin.model.AdminTableLoad;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface AdminTableLoadRepository extends JpaRepository<AdminTableLoad, Integer> {

}
