export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  const encoded = encodeURIComponent(keyword.trim());
  const endpoint = keyword.trim().endsWith('*')
    ? `${CLIENT_SYSPRIN_READER_API_BASE}/client/client/wildcard?keyword=${encoded}`
    : `${CLIENT_SEARCH_INTEGRATION_API_BASE}/report-autocomplete?keyword=${encoded}`;
  
  const res = await fetch(endpoint);
  if (!res.ok) throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  return res.json();
};
