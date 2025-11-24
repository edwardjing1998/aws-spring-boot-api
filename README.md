package rapid.searchintegration.service;

import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.TextField;
import org.apache.lucene.index.DirectoryReader;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriterConfig;
import org.apache.lucene.index.Term;
import org.apache.lucene.queryparser.classic.MultiFieldQueryParser;
import org.apache.lucene.search.*;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.RAMDirectory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import rapid.dto.client.ClientSearchDTO;
import rapid.model.client.Client;

import java.io.IOException;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Component
public class ClientIndexer {
    private static final Logger logger = LoggerFactory.getLogger(ClientIndexer.class);

    private final Directory memoryIndex = new RAMDirectory();
    private final StandardAnalyzer analyzer = new StandardAnalyzer();
    private boolean indexInitialized = false; // 👈 ensures search only works after some indexing

    // ---------- helper: build Lucene document ----------
    private Document buildDocument(Client client) {
        Document doc = new Document();
        // you can add more fields later if needed
        doc.add(new TextField("client", client.getClient(), Field.Store.YES));
        doc.add(new TextField("name", client.getName(), Field.Store.YES));
        return doc;
    }

    // ---------- full rebuild (called at startup) ----------
    public void indexClients(List<Client> clients) throws Exception {
        IndexWriterConfig config = new IndexWriterConfig(analyzer);
        try (IndexWriter writer = new IndexWriter(memoryIndex, config)) {
            writer.deleteAll();
            for (Client client : clients) {
                Document doc = buildDocument(client);
                writer.addDocument(doc);
            }
            writer.commit();
        }
        indexInitialized = true;
        logger.info("Lucene indexing completed. Total indexed: {}", clients.size());
    }

    // ---------- incremental index (create / update one client) ----------
    public void indexClient(Client client) throws Exception {
        IndexWriterConfig config = new IndexWriterConfig(analyzer);
        try (IndexWriter writer = new IndexWriter(memoryIndex, config)) {
            // update based on unique key "client"
            writer.updateDocument(new Term("client", client.getClient()), buildDocument(client));
            writer.commit();
        }
        indexInitialized = true; // in case this is the first time
        logger.info("Lucene index updated for client: {}", client.getClient());
    }

    // ---------- delete one client from the index ----------
    public void deleteClient(String clientCode) throws Exception {
        if (!indexInitialized) {
            // nothing to delete yet; avoid error
            logger.warn("Attempted to delete client {} but index not initialized.", clientCode);
            return;
        }
        IndexWriterConfig config = new IndexWriterConfig(analyzer);
        try (IndexWriter writer = new IndexWriter(memoryIndex, config)) {
            writer.deleteDocuments(new Term("client", clientCode));
            writer.commit();
        }
        logger.info("Lucene index deleted client: {}", clientCode);
    }

    // ---------- search ----------
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
                return results;
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
