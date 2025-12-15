/* ---------------- fetch utility ---------------- */
async function request<T = any>(url: string, opts: RequestInit = {}): Promise<T> {
  const res = await fetch(url, opts);
  if (!res.ok) throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  return res.json();
}

export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  const encoded = encodeURIComponent(keyword.trim());
  // Directly use the search-integration endpoint for all queries
  const endpoint = `http://localhost:8089/search-integration/api/report-autocomplete?keyword=${encoded}`;
  return request(endpoint);
};
