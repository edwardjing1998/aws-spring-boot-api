@Transactional
public boolean deleteAdminQueryListAndC3FileTransfer(Integer reportId) {
    Optional<AdminQueryList> optionalAdminQuery = adminQueryListRepository.findById(reportId);

    if (optionalAdminQuery.isPresent()) {
        AdminQueryList adminQuery = optionalAdminQuery.get();

        // Step 1: delete C3FileTransfer manually
        C3FileTransfer relatedTransfer = adminQuery.getC3FileTransferParam();
        if (relatedTransfer != null) {
            c3FileTransferRepository.delete(relatedTransfer);
        }

        // Step 2: delete AdminQueryList
        adminQueryListRepository.delete(adminQuery);

        return true;
    }

    return false;
}
