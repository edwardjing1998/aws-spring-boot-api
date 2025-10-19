package rapid.model.sysprin.key;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;
import java.util.Objects;

@Embeddable
@Data
@NoArgsConstructor
@AllArgsConstructor
public class InvalidDelivAreaId implements Serializable {

    @Column(name = "SYS_PRIN", nullable = false, length = 12)
    private String sysPrin;

    @Column(name = "AREA", nullable = false, length = 3)
    private String area;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof InvalidDelivAreaId)) return false;
        InvalidDelivAreaId that = (InvalidDelivAreaId) o;
        return Objects.equals(sysPrin, that.sysPrin) &&
                Objects.equals(area, that.area);
    }

    @Override
    public int hashCode() {
        return Objects.hash(sysPrin, area);
    }
}



package rapid.model.sysprin;

import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.sysprin.base.BaseInvalidDelivArea;

@Entity
@Table(name = "INVALID_DELIV_AREAS")
@NoArgsConstructor
public class InvalidDelivArea extends BaseInvalidDelivArea {
}


package rapid.model.sysprin.base;

import jakarta.persistence.EmbeddedId;
import jakarta.persistence.MappedSuperclass;
import lombok.Data;
import rapid.model.sysprin.key.InvalidDelivAreaId;

@MappedSuperclass
@Data
public abstract class BaseInvalidDelivArea {
    @EmbeddedId
    private InvalidDelivAreaId id = new InvalidDelivAreaId();
}












