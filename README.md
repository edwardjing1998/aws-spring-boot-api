List<Integer> relevantReportIds = optionPage.stream()
            // ✅ Correct: Access the embedded ID, then the report ID
            .map(option -> option.getId().getReportId()) 
            .distinct()
            .collect(Collectors.toList());
