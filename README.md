package rapid.cases.web;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import rapid.cases.config.GlobalExceptionHandler;
import rapid.cases.service.CaseArchivalService;
import rapid.cases.service.CaseService;
import rapid.dto.cases.CaseDTO;

import java.util.List;

import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

class CaseWriterControllerTest {

    private MockMvc mockMvc;

    @Mock
    private CaseService caseService;

    @Mock
    private CaseArchivalService archivalService;

    @InjectMocks
    private CaseWriterController controller;

    private final ObjectMapper objectMapper = new ObjectMapper();

    @BeforeEach
    void setup() {
        MockitoAnnotations.openMocks(this);
        mockMvc = MockMvcBuilders.standaloneSetup(controller)
                .setControllerAdvice(new GlobalExceptionHandler())
                .build();
    }

    @Test
    void testGetCaseByNumber_returnsOk() throws Exception {
        String account = "ACC123";

        CaseDTO dto = CaseDTO.builder()
                .caseNumber("CASE123")
                .account("ACC123")
                .firstName("John")
                .lastName("Doe")
                .active(true)
                .build();

        when(caseService.getCasesByAccount(account)).thenReturn(List.of(dto));

        mockMvc.perform(get("/cases/{accountNumber}", account))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].caseNumber").value("CASE123"))
                .andExpect(jsonPath("$[0].firstName").value("John"))
                .andExpect(jsonPath("$[0].lastName").value("Doe"));

        verify(caseService, times(1)).getCasesByAccount(account);
    }

    @Test
    void testGetCaseByNumber_notFound() throws Exception {
        String account = "ACC404";
        when(caseService.getCasesByAccount(account)).thenReturn(List.of());

        mockMvc.perform(get("/cases/{accountNumber}", account))
                .andExpect(status().isNotFound());
    }

    @Test
    void testArchiveAddressesForCases_with_success() throws Exception {
        String account = "ACC123";

        mockMvc.perform(delete("/cases/delete")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(account))
                .andExpect(status().isOk());

        verify(archivalService).archiveAndDeleteCases(account);
    }


    @Test
    void testCreateCase_success() throws Exception {
        CaseDTO inputDto = CaseDTO.builder()
                .caseNumber("CASE123")
                .account("ACC123")
                .firstName("Jane")
                .lastName("Smith")
                .active(true)
                .build();

        when(caseService.createCase(any(CaseDTO.class))).thenReturn(inputDto);

        mockMvc.perform(post("/case/create")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(inputDto)))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.message").value("Case created successfully."))
                .andExpect(jsonPath("$.total").value(1))
                .andExpect(jsonPath("$.caseNumbers[0]").value("CASE123"));

        verify(caseService, times(1)).createCase(any(CaseDTO.class));
    }

    @Test
    void testUpdateCase_success() throws Exception {
        String caseNumber = "CASE456";

        CaseDTO inputDto = CaseDTO.builder()
                .caseNumber(caseNumber)
                .account("ACC456")
                .firstName("Alice")
                .lastName("Johnson")
                .active(true)
                .build();

        when(caseService.updateCase(eq(caseNumber), any(CaseDTO.class))).thenReturn(inputDto);

        mockMvc.perform(put("/case/{caseNumber}", caseNumber)
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(inputDto)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.message").value("Case updated successfully."))
                .andExpect(jsonPath("$.total").value(1))
                .andExpect(jsonPath("$.caseNumbers[0]").value(caseNumber));

        verify(caseService, times(1)).updateCase(eq(caseNumber), any(CaseDTO.class));
    }
}


package rapid.cases.web;

import io.swagger.v3.oas.annotations.Parameter;
import jakarta.validation.Valid;
import jakarta.validation.constraints.NotBlank;
import lombok.AllArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.common.util.ApiResponseUtil;
import rapid.dto.cases.CaseDTO;
import rapid.cases.exception.CaseNotFoundException;
import rapid.cases.service.CaseArchivalService;
import rapid.cases.service.CaseService;
import rapid.dto.cases.response.CaseWriterApiResponse;

import java.util.List;

@RestController
@CrossOrigin(origins = "*")
@AllArgsConstructor
@Slf4j
public class CaseWriterController {

    private final CaseService caseService;
    private final CaseArchivalService archivalService;

    @GetMapping("/cases/{accountNumber}")
    public ResponseEntity<List<CaseDTO>> getCaseByNumber (
            @Parameter(description = "Account number to retrieve deleted cases for", required = true)
            @PathVariable @NotBlank String accountNumber) {

        log.info("GET /cases/{}", accountNumber);

        List<CaseDTO> dtos = caseService.getCasesByAccount(accountNumber);

        log.info("record size {}", dtos.size());

        if (dtos.isEmpty()) {
            throw new CaseNotFoundException("No cases found for account: " + accountNumber);
        }

        return ApiResponseUtil.okList(dtos);
    }

    @DeleteMapping("/cases/delete")
    public ResponseEntity<CaseWriterApiResponse> archiveAddressesForCases(@RequestBody String accountNumber) {
        log.info("DELETE /cases/delete – account number {}", accountNumber);

        List<String> deletedCases = archivalService.archiveAndDeleteCases(accountNumber);
        return ApiResponseUtil.deleted("Cases archived and deleted successfully.", deletedCases);
    }

    @PostMapping("/case/create")
    public ResponseEntity<CaseWriterApiResponse> createCase(@RequestBody @Valid CaseDTO caseDTO) {
        CaseDTO saved = caseService.createCase(caseDTO);
        return ApiResponseUtil.created("Case created successfully.", saved.getCaseNumber());
    }

    @PutMapping("/case/{caseNumber}")
    public ResponseEntity<CaseWriterApiResponse> updateCase(
            @PathVariable String caseNumber,
            @RequestBody @Valid CaseDTO dto) {

        CaseDTO updated = caseService.updateCase(caseNumber, dto);
        return ApiResponseUtil.updated("Case updated successfully.", updated.getCaseNumber());
    }

    @PutMapping("/case/{accNum}")
    public ResponseEntity<CaseWriterApiResponse> updateUserCase(@RequestBody @Valid CaseDTO dto) {
        CaseDTO updated = caseService.updateUserCase(dto);
        return ApiResponseUtil.updated("Case updated successfully.", updated.getCaseNumber());
    }

}




