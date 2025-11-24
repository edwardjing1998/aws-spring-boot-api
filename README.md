@RestController
@RequestMapping("/api/clients")
@RequiredArgsConstructor
public class ClientController {

    private final ClientRepository clientRepository;
    private final SearchClientService searchClientService;

    @PostMapping
    public ResponseEntity<Client> createClient(@RequestBody Client client) {
        Client saved = clientRepository.save(client);
        searchClientService.indexClient(saved);  // 🔔 update index
        return ResponseEntity.ok(saved);
    }

    @DeleteMapping("/{clientCode}")
    public ResponseEntity<Void> deleteClient(@PathVariable String clientCode) {
        clientRepository.deleteByClient(clientCode);  // your existing delete
        searchClientService.deleteClientFromIndex(clientCode);  // 🔔 remove index entry
        return ResponseEntity.noContent().build();
    }
}
