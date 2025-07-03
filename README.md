package rapid.dto.cases;

import lombok.AllArgsConstructor;
import lombok.Data;

import java.util.List;

@Data
@AllArgsConstructor
public class DeleteCaseApiResponse {
    private String message;
    private int archivedCount;
    private List<String> deletedCaseNumbers;

}
