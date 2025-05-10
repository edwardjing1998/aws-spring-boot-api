package admin.model;

import jakarta.persistence.*;

import java.time.LocalDateTime;

@Entity
@Table(name = "admin_query_data_def")
public class AdminDataDefinitionsList {

    @Id
    @Column(name = "report_id")
    private Integer reportId;

    @Column(name = "input0_data_mask", nullable = true, length = 50)
    private String input0DataMask;

    @Column(name = "input0_data_type", nullable = true, length = 5)
    private String input0DataType;

    @Column(name = "input0_data_length", nullable = true, length = 5)
    private String input0DataLength;

    @Column(name = "input1_data_mask", nullable = true, length = 50)
    private String input1DataMask;

    @Column(name = "input1_data_type", nullable = true, length = 5)
    private String input1DataType;

    @Column(name = "input1_data_length", nullable = true, length = 5)
    private String input1DataLength;

    @Column(name = "input2_data_mask", nullable = true, length = 50)
    private String input2DataMask;

    @Column(name = "input2_data_type", nullable = true, length = 5)
    private String input2DataType;

    @Column(name = "input2_data_length", nullable = true, length = 5)
    private String input2DataLength;

    @Column(name = "input3_data_mask", nullable = true, length = 50)
    private String input3DataMask;

    @Column(name = "input3_data_type", nullable = true, length = 5)
    private String input3DataType;

    @Column(name = "input3_data_length", nullable = true, length = 5)
    private String input3DataLength;

    @Column(name = "input4_data_mask", nullable = true, length = 50)
    private String input4DataMask;

    @Column(name = "input4_data_type", nullable = true, length = 5)
    private String input4DataType;

    @Column(name = "input4_data_length", nullable = true, length = 5)
    private String input4DataLength;

    @Column(name = "input5_data_mask", nullable = true, length = 50)
    private String input5DataMask;

    @Column(name = "input5_data_type", nullable = true, length = 5)
    private String input5DataType;

    @Column(name = "input5_data_length", nullable = true, length = 5)
    private String input5DataLength;

    @Column(name = "input6_data_mask", nullable = true, length = 50)
    private String input6DataMask;

    @Column(name = "input6_data_type", nullable = true, length = 5)
    private String input6DataType;

    @Column(name = "input6_data_length", nullable = true, length = 5)
    private String input6DataLength;

    @Column(name = "input7_data_mask", nullable = true, length = 50)
    private String input7DataMask;

    @Column(name = "input7_data_type", nullable = true, length = 5)
    private String input7DataType;

    @Column(name = "input7_data_length", nullable = true, length = 5)
    private String input7DataLength;

    @Column(name = "input8_data_mask", nullable = true, length = 50)
    private String input8DataMask;

    @Column(name = "input8_data_type", nullable = true, length = 5)
    private String input8DataType;

    @Column(name = "input8_data_length", nullable = true, length = 5)
    private String input8DataLength;

    public AdminDataDefinitionsList() {

    }

    public Integer getReportId() { return reportId; }

    public void setReportId(Integer reportId) { this.reportId = reportId; }

    public String getInput0DataMask() { return input0DataMask; }

    public void setInput0DataMask(String input0DataMask) {
        this.input0DataMask = input0DataMask;
    }

    public String getInput0DataType() { return input0DataType; }

    public void setInput0DataType(String input0DataType) {
        this.input0DataType = input0DataType;
    }

    public String getInput0DataLength() { return input0DataLength; }

    public void setInput0DataLength(String input0DataLength) {
        this.input0DataLength = input0DataLength;
    }

    public String getInput1DataMask() { return input1DataMask; }

    public void setInput1DataMask(String input1DataMask) {

        this.input1DataMask = input1DataMask;
    }

    public String getInput1DataType() { return input1DataType; }

    public void setInput1DataType(String input1DataType) {
        this.input1DataType = input1DataType;
    }

    public String getInput1DataLength() { return input1DataLength; }

    public void setInput1DataLength(String input1DataLength) {
        this.input1DataLength = input1DataLength;
    }

    public String getInput2DataMask() { return input2DataMask; }

    public void setInput2DataMask(String input2DataMask) {
        this.input2DataMask = input2DataMask;
    }

    public String getInput2DataType() { return input2DataType; }

    public void setInput2DataType(String input2DataType) {
        this.input2DataType = input2DataType;
    }

    public String getInput2DataLength() { return input2DataLength; }

    public void setInput2DataLength(String input2DataLength) {
        this.input2DataLength = input2DataLength;
    }

    public String getInput3DataMask() { return input3DataMask; }

    public void setInput3DataMask(String input3DataMask) {
        this.input3DataMask = input3DataMask;
    }

    public String getInput3DataType() { return input3DataType; }

    public void setInput3DataType(String input3DataType) {
        this.input3DataType = input3DataType;
    }

    public String getInput3DataLength() { return input3DataLength; }

    public void setInput3DataLength(String input3DataLength) {
        this.input3DataLength = input3DataLength;
    }
    public String getInput4DataMask() { return input4DataMask; }

    public void setInput4DataMask(String input4DataMask) {
        this.input4DataMask = input4DataMask;
    }

    public String getInput4DataType() { return input4DataType; }


    public void setInput4DataType(String input4DataType) {
        this.input4DataType = input4DataType;
    }

    public String getInput4DataLength() { return input4DataLength; }

    public void setInput4DataLength(String input4DataLength) {
        this.input4DataLength = input4DataLength;
    }

    public String getInput5DataMask() { return input5DataMask; }

    public void setInput5DataMask(String input5DataMask) {
        this.input5DataMask = input5DataMask;
    }

    public String getInput5DataType() { return input5DataType; }

    public void setInput5DataType(String input5DataType) {
        this.input5DataType = input5DataType;
    }

    public String getInput5DataLength() { return input5DataLength; }

    public void setInput5DataLength(String input5DataLength) {
        this.input5DataLength = input5DataLength;
    }

    public String getInput6DataMask() { return input6DataMask; }

    public void setInput6DataMask(String input6DataMask) {
        this.input6DataMask = input6DataMask;
    }
    public String getInput6DataType() { return input6DataType; }

    public void setInput6DataType(String input6DataType) {
        this.input6DataType = input6DataType;
    }

    public String getInput6DataLength() { return input6DataLength; }

    public void setInput6DataLength(String input6DataLength) {
        this.input6DataLength = input6DataLength;
    }

    public String getInput7DataMask() { return input7DataMask; }

    public void setInput7DataMask(String input7DataMask) {
        this.input7DataMask = input7DataMask;
    }

    public String getInput7DataType() { return input7DataType; }

    public void setInput7DataType(String input7DataType) {
        this.input7DataType = input7DataType;
    }

    public String getInput7DataLength() { return input7DataLength; }

    public void setInput7DataLength(String input7DataLength) {
        this.input7DataLength = input7DataLength;
    }

    public String getInput8DataMask() { return input8DataMask; }

    public void setInput8DataMask(String input8DataMask) {
        this.input8DataMask = input8DataMask;
    }
    public String getInput8DataType() { return input8DataType; }

    public void setInput8DataType(String input8DataType) {
        this.input8DataType = input8DataType;
    }

    public String getInput8DataLength() { return input8DataLength; }

    public void setInput8DataLength(String input8DataLength) {
        this.input8DataLength = input8DataLength;
    }
}
