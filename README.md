export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  if (!keyword || !keyword.trim()) return [];

  const encoded = encodeURIComponent(keyword.trim());
  // Removed wildcard logic, always use report-autocomplete
  const path = `/search-integration/api/report-autocomplete?keyword=${encoded}`;
  
  const url = new URL(path, GATEWAY_BASE_URL).toString();
  return fetchJson(url, { method: 'GET' });
};
