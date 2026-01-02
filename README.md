export const fetchPreviewAtmCashPrefixes = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<AtmCashPrefixRow[]> => {
  try {

    alert("clientId preview " + clientId);

    const url = `${CLIENT_SYSPRIN_READER_API_BASE}/client/sysprin-prefix/${encodeURIComponent(clientId)}/pagination?page=${page}&size=${pageSize}`;

    alert("url => " + url);

        alert("OK ? ");

    const resp = await fetch(url, {
      method: 'GET',
      headers: { accept: '*/*' },
    });

        alert("OK ? " + resp);


    if (resp.ok) {
      const json = await resp.json();
            alert("json " + json);

      // Normalize response: handle direct array or Page content object
      const nextRows: AtmCashPrefixRow[] = Array.isArray(json) ? json : (json.content || []);
      return nextRows;
    } else {
      alert("errors ");
      console.error("Failed to fetch prefixes");
      return [];
    }
  } catch (error) {
    console.error("Error fetching prefixes:", error);
    return [];
  }
};





package client.reader.client.web;

import common.exception.sysprin.SysPrinBadRequestException;
import common.service.client.SysPrinsPrefixService;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.Size;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import trace.client.mapper.fetch.SysPrinsPrefixDataBundle;
import common.service.client.fetcher.SysPrinsPrefixDataFetcher;
import trace.dto.client.SysPrinsPrefixDTO;

import java.util.List;

@RestController
@RequestMapping("/v1/client/sysprin-prefix")
@RequiredArgsConstructor
@Slf4j
public class SysPrinsPrefixController {

    private final SysPrinsPrefixDataFetcher dataFetcher;

    /**
     * Get all SysPrinsPrefix records grouped by billingSp.
     */
    @GetMapping("/{billingSp}/pagination")
    public ResponseEntity<List<SysPrinsPrefixDTO>> getSysPrinsPrefixesByBillingSp(
            @PathVariable @Size(max = 8, message = "ClientId should not be more than 8 characters") String billingSp,
            @RequestParam(defaultValue = "0") @Min(0) int page,
            @RequestParam(defaultValue = "20") @Min(1) int size) {
        List<SysPrinsPrefixDTO> prefixes = dataFetcher.fetchByBillingSpPagination(billingSp, page, size);
        log.info("the count {} found for {}", prefixes.size(), billingSp);
        return ResponseEntity.ok(prefixes);
    }

}





package gateway;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = {"*"})
@EntityScan(basePackages = "trace.model")
@EnableJpaRepositories(basePackages = {"trace.repository", "search.integration"})
@ComponentScan(
    basePackages = {
        "client.reader",
        "client.writer",
        "trace",
        "common"
    }
)
@SpringBootApplication
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}






