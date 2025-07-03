    @DeleteMapping("/cases/delete")
    public ResponseEntity<DeleteCaseApiResponse> archiveAddressesForCases(@RequestBody String accountNumber) {
        log.info("DELETE /cases/delete – account number {}", accountNumber);

        List<String> deletedCases = archivalService.archiveAndDeleteCases(accountNumber); // return caseNumbers
        DeleteCaseApiResponse response = new DeleteCaseApiResponse(
                "Cases archived and deleted successfully.",
                deletedCases.size(),
                deletedCases
        );
        return ResponseEntity.ok(response);
    }
