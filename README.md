package admin.model;


import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@Table(name = "column_mapping")
@Data
@NoArgsConstructor
public class AdminTableLoadColumnMapping {

    @EmbeddedId
    private ReportIdforColumnMapping reportId;

    @Column(name = "Is_Numeric")
    private Byte isNumeric;
}





package admin.model;

import jakarta.persistence.Embeddable;

import java.io.Serializable;
import java.util.Objects;

@Embeddable
public class ReportIdforColumnMapping implements Serializable {

    private Integer reportId;
    private String fromColName;
    private String toColName;

    public ReportIdforColumnMapping(){

    }

    public ReportIdforColumnMapping(Integer reportId, String fromColName, String toColName){
        this.reportId = reportId;
        this.fromColName = fromColName;
        this.toColName = toColName;
    }

    public Integer getReportId() {
        return reportId;
    }

    public void setReportId(Integer reportId) {
        this.reportId = reportId;
    }

    public String getFromColName() {
        return fromColName;
    }

    public void setFromColName(String fromColName) {
        this.fromColName = fromColName;
    }

    public String getToColName() {
        return toColName;
    }

    public void setToColName(String toColName) {
        this.toColName = toColName;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof ReportIdforColumnMapping)) return false;
        ReportIdforColumnMapping obj = (ReportIdforColumnMapping) o;
        return Objects.equals(reportId, obj.reportId) &&
                Objects.equals(fromColName, obj.fromColName) &&
                Objects.equals(toColName, obj.toColName);
    }

    @Override
    public int hashCode() {
        return Objects.hash(reportId, fromColName, toColName);
    }

}
