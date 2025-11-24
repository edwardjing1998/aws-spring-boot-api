package rapid.searchintegration.web;

import lombok.AllArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.dto.client.ClientSearchDTO;
import rapid.searchintegration.service.SearchClientService;

import java.util.List;

@RestController
@RequestMapping("/api")
@AllArgsConstructor
public class SearchClientController {

    private final SearchClientService clientService;

    @GetMapping("/client-autocomplete")
    public ResponseEntity<List<ClientSearchDTO>> autocomplete(@RequestParam String keyword) {
        try {
            List<ClientSearchDTO> results = clientService.getClientSearch(keyword);
            if (results == null || results.isEmpty()) {
                return ResponseEntity.ok(
                        List.of(new ClientSearchDTO(keyword, "client not found"))
                );
            }
            return ResponseEntity.ok(results);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body(List.of(new ClientSearchDTO("error", e.getMessage())));
        }
    }
}



package rapid.searchintegration.service;

import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.TextField;
import org.apache.lucene.index.DirectoryReader;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriterConfig;
import org.apache.lucene.queryparser.classic.MultiFieldQueryParser;
import org.apache.lucene.search.*;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.RAMDirectory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import rapid.dto.client.ClientSearchDTO;
import rapid.model.client.Client;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Component
public class ClientIndexer {
    private static final Logger logger = LoggerFactory.getLogger(ClientIndexer.class);
    private final Directory memoryIndex = new RAMDirectory();
    private final StandardAnalyzer analyzer = new StandardAnalyzer();
    private boolean indexInitialized = false; // 👈 flag to ensure search only works after indexing

    public void indexClients(List<Client> clients) throws Exception {
        IndexWriterConfig config = new IndexWriterConfig(analyzer);
        try (IndexWriter writer = new IndexWriter(memoryIndex, config)) {
            writer.deleteAll();
            for (Client client : clients) {
                Document doc = new Document();

                doc.add(new TextField("client", client.getClient(), Field.Store.YES));
                doc.add(new TextField("name", client.getName(), Field.Store.YES));

                writer.addDocument(doc);
            }
            writer.commit();
        }
        indexInitialized = true;
        logger.info("Lucene indexing completed. Total indexed: {}", clients.size());
    }

    public List<ClientSearchDTO> searchClients(String keyword) throws Exception {
        if (!indexInitialized) {
            throw new IllegalStateException("Lucene index is not initialized yet.");
        }

        List<ClientSearchDTO> results = new ArrayList<>();
        Set<String> seen = new HashSet<>();

        String escapedKeyword = MultiFieldQueryParser.escape(keyword);
        Query query = new MultiFieldQueryParser(
                new String[]{"client", "name"}, analyzer
        ).parse(escapedKeyword + "*");

        try (DirectoryReader reader = DirectoryReader.open(memoryIndex)) {
            IndexSearcher searcher = new IndexSearcher(reader);

            TotalHitCountCollector countCollector = new TotalHitCountCollector();
            searcher.search(query, countCollector);
            int totalHits = countCollector.getTotalHits();

            if (totalHits == 0) {
                return results; // 👈 return empty list
            }

            TopDocs topDocs = searcher.search(query, totalHits);

            for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
                Document doc = searcher.doc(scoreDoc.doc);
                String client = doc.get("client");
                String name = doc.get("name");

                String key = client + "::" + name;

                if (seen.add(key)) {
                    results.add(new ClientSearchDTO(client, name));
                }
            }
        }

        return results;
    }
}


package rapid.searchintegration.service;

import jakarta.annotation.PostConstruct;
import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;
import rapid.dto.client.ClientSearchDTO;
import rapid.repository.client.ClientRepository;

import java.util.List;
import java.util.stream.Collectors;

@Service
@AllArgsConstructor
public class SearchClientService {
    private final ClientRepository clientRepository;
    private final ClientIndexer luceneIndexer;

    @PostConstruct
    public void initLucene() throws Exception {
        luceneIndexer.indexClients(clientRepository.findAllValidClients());
    }

    public List<ClientSearchDTO> getClientSearch(String keyword) throws Exception {
        return luceneIndexer.searchClients(keyword).stream()
                .map(c -> new ClientSearchDTO(c.getClient(), c.getName()))
                .collect(Collectors.toList());
    }
}
