package rapid.delete.cases.dto;

import java.util.List;

public class ApiResponse {

    private String message;
    private int archivedCount;
    private List<String> deletedCaseNumbers;

    // Constructor
    public ApiResponse(String message, int archivedCount, List<String> deletedCaseNumbers) {
        this.message = message;
        this.archivedCount = archivedCount;
        this.deletedCaseNumbers = deletedCaseNumbers;
    }

    // Getters
    public String getMessage() {
        return message;
    }

    public int getArchivedCount() {
        return archivedCount;
    }

    public List<String> getDeletedCaseNumbers() {
        return deletedCaseNumbers;
    }

    // Setters
    public void setMessage(String message) {
        this.message = message;
    }

    public void setArchivedCount(int archivedCount) {
        this.archivedCount = archivedCount;
    }

    public void setDeletedCaseNumbers(List<String> deletedCaseNumbers) {
        this.deletedCaseNumbers = deletedCaseNumbers;
    }

    // Optional: toString() for logging or debugging
    @Override
    public String toString() {
        return "ApiResponse{" +
                "message='" + message + '\'' +
                ", archivedCount=" + archivedCount +
                ", deletedCaseNumbers=" + deletedCaseNumbers +
                '}';
    }
}
