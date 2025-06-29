package rapid.delete.cases.dto;

public class DeleteCaseResponse {
    private String message;
    private int archivedCount;
    private List<String> deletedCaseNumbers;

    public DeleteCaseResponse(String message, int archivedCount, List<String> deletedCaseNumbers) {
        this.message = message;
        this.archivedCount = archivedCount;
        this.deletedCaseNumbers = deletedCaseNumbers;
    }

    // Getters and Setters
}
