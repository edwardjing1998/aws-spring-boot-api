package rapid.delete.cases.dto;

import lombok.AllArgsConstructor;
import lombok.Data;

import java.util.List;

@Data
@AllArgsConstructor
public class ApiResponse {
    private String message;
    private int archivedCount;
    private List<String> deletedCaseNumbers;
}
