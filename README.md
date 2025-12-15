export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  const encoded = encodeURIComponent(keyword.trim());
  const endpoint = `http://localhost:8089/search-integration/api/report-autocomplete?keyword=${encoded}`;

  const res = await fetch(endpoint);
  
  if (!res.ok) {
    throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  }
  
  return res.json();
};
