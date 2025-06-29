@DeleteMapping("/delete")
public ResponseEntity<ApiResponse> archiveAddressesForCases(@RequestBody String accountNumber) {
    log.info("DELETE /cases/delete – account number {}", accountNumber);

    List<String> deletedCases = archivalService.archiveAndDeleteCases(accountNumber); // return caseNumbers
    ApiResponse response = new ApiResponse(
            "Cases archived and deleted successfully.",
            deletedCases.size(),
            deletedCases
    );
    return ResponseEntity.ok(response);
}
