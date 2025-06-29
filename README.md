git add .
PS C:\Users\F2LIPBX\spring_boot\rapid-case-service> git commit -m "rebase"
[detached HEAD e2b3a57] rebase
 Committer: Jing <guoxiu.jing@fiserv.com>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly. Run the
following command and follow the instructions in your editor to edit
your configuration file:

    git config --global --edit

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 147 files changed, 5956 insertions(+), 77 deletions(-)
 create mode 100644 .github/workflows/maven.yml
 create mode 100644 .gitignore
 create mode 100644 Dockerfile
 create mode 100644 Dockerfile-Fat
 create mode 100644 common-api-dto/pom.xml
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/AccountTransactionDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/AddressDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/BulkCardDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/CaseDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/DeleteCaseApiResponse.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/FailedTransactionDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/LabelsDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/TransactionDTO.java
 create mode 100644 common-api-dto/src/main/java/rapid/dto/cases/error/ApiError.java
 create mode 100644 common-mapper/pom.xml
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/AccountTransactionMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/AddressMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/BulkCardMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/CaseMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/FailedTransactionMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/LabelMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/TransactionMapper.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/fetch/CaseDataBundle.java
 create mode 100644 common-mapper/src/main/java/rapid/cases/mapper/fetch/DltCaseDataBundle.java
 create mode 100644 common-mapper/src/test/java/rapid/cases/mapper/CaseMapperTest.java
 create mode 100644 common-mapper/src/test/java/rapid/cases/mapper/fetch/CaseDataBundleTest.java
 create mode 100644 common-mapper/src/test/java/rapid/cases/mapper/fetch/DltCaseDataBundleTest.java
 create mode 100644 common-model/pom.xml
 create mode 100644 common-model/src/main/java/rapid/CasesModelApplication.java
 create mode 100644 common-model/src/main/java/rapid/config/SwaggerConfig.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/accountTransaction/AccountTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/accountTransaction/DltAccountTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/address/Address.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/address/DltAddress.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseAccountTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseAddress.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseBulkCard.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseCase.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseFailedTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseLabels.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/BaseTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/base/CaseNumberHolder.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/bulkCard/BulkCard.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/bulkCard/DltBulkCard.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/cases/Case.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/cases/DltCase.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/failedTransaction/DltFailedTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/failedTransaction/FailedTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/AccountTransactionId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/AddressId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/BulkCardId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/FailedTransactionId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/LabelsId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/key/TransactionId.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/label/DltLabels.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/label/Labels.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/transaction/DltTransaction.java
 create mode 100644 common-model/src/main/java/rapid/model/cases/transaction/Transaction.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/accountTransaction/AccountTransactionRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/accountTransaction/DltAccountTransactionRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/address/AddressRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/address/DltAddressRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/base/BaseCaseNumberRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/bulkCard/BulkCardRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/bulkCard/DltBulkCardRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/cases/CaseRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/cases/DltCaseRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/failedTransaction/DltFailedTransactionRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/failedTransaction/FailedTransactionRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/label/DltLabelsRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/label/LabelsRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/transaction/DltTransactionRepo.java
 create mode 100644 common-model/src/main/java/rapid/repository/cases/transaction/TransactionRepo.java
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-account-transaction-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-addresses-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-bulk-card-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-cases-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-failed-trans-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-labels-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/cases/create-transactions-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/db.changelog-master.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-delete-failed-trans-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-account-transaction-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-addresses-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-bulk-card-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-cases-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-labels-table.xml
 create mode 100644 common-model/src/main/resources/db/changelog/deleted-cases/create-deleted-transactions-table.xml
 create mode 100644 common-model/src/test/java/rapid/model/cases/CasesModelApplicationTests.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/DltCaseAddressMappingTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/TestApp.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseAccountTransactionTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseAddressTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseBulkCardTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseCaseTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseFailedTransactionTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseLabelsTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/base/BaseTransactionTest.java
 create mode 100644 common-model/src/test/java/rapid/model/cases/config/SwaggerConfigTest.java
 create mode 100644 common-model/src/test/resources/application-test.yml
 create mode 100644 delete-case/Dockerfile
 create mode 100644 delete-case/pom.xml
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/DeleteCaseApplication.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/config/GlobalExceptionHandler.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/config/data/CaseDataGenerator.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/exception/CaseNotFoundException.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/service/CaseArchivalService.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/service/CaseService.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/service/fetch/CaseDataFetcher.java
 create mode 100644 delete-case/src/main/java/rapid/delete/cases/web/DeleteCaseController.java
 create mode 100644 delete-case/src/main/resources/application-dev.yml
 create mode 100644 delete-case/src/main/resources/application-local.yml
 create mode 100644 delete-case/src/main/resources/application.yml
 create mode 100644 delete-case/src/test/java/rapid/delete/cases/service/CaseArchivalServiceTest.java
 create mode 100644 delete-case/src/test/java/rapid/delete/cases/service/CaseService.java
 create mode 100644 delete-case/src/test/java/rapid/delete/cases/service/fetch/CaseDataFetcherTest.java
 create mode 100644 delete-case/src/test/java/rapid/delete/cases/web/DeleteCaseControllerTest.java
 create mode 100644 docker-compose.yml
 create mode 100644 gateway/Dockerfile
 create mode 100644 gateway/pom.xml
 create mode 100644 gateway/src/main/java/gateway/GatewayApplication.java
 create mode 100644 gateway/src/main/java/gateway/config/GatewayRoutesConfig.java
 create mode 100644 gateway/src/main/resources/application.yml
 create mode 100644 img.png
 create mode 100644 img_1.png
 create mode 100644 img_2.png
 create mode 100644 img_3.png
 create mode 100644 pom.xml
 create mode 100644 review-deleted-case/Dockerfile
 create mode 100644 review-deleted-case/pom.xml
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/ReviewDltCaseApplication.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/config/data/DltCaseDataGenerator.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/exception/ApiError.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/exception/CaseNotFoundException.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/exception/GlobalExceptionHandler.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/service/DltCaseService.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/service/fetch/DltCaseDataFetcher.java
 create mode 100644 review-deleted-case/src/main/java/rapid/review/deletedcase/web/ReviewDltCaseController.java
 create mode 100644 review-deleted-case/src/main/resources/application.yml
 create mode 100644 service-c/Dockerfile
 create mode 100644 service-c/pom.xml
 create mode 100644 service-c/src/main/java/serviceC/ServiceCApplication.java
 create mode 100644 service-c/src/main/java/serviceC/web/HelloCController.java
 create mode 100644 service-c/src/main/resources/application.yml
 create mode 100644 src/main/java/org/example/Main.java
 create mode 100644 supervisord.conf
PS C:\Users\F2LIPBX\spring_boot\rapid-case-service> git push
fatal: You are not currently on a branch.
To push the history leading to the current (detached HEAD)
state now, use

    git push origin HEAD:<name-of-remote-branch>

PS C:\Users\F2LIPBX\spring_boot\rapid-case-service> git status
interactive rebase in progress; onto 3177721
Last command done (1 command done):
   pick dda1ec0 # delete case user story
Next commands to do (2 remaining commands):
   pick f4c6f38 # merge readme.md
   pick e3b610e # readme
  (use "git rebase --edit-todo" to view and edit)
You are currently editing a commit while rebasing branch 'rapid-case-service-j' on '3177721'.
  (use "git commit --amend" to amend the current commit)
  (use "git rebase --continue" once you are satisfied with your changes)

nothing to commit, working tree clean

