// const baseURL = 'http://localhost:4444/api';
const baseURL = 'http://localhost:8089/client-sysprin-reader/api/client';


export const fetchClientsPaging = async (page: number, size: number): Promise<any> => {
  //    const response = await fetch(`${baseURL}/clients-paging?page=${page}&size=${size}`);
  const response = await fetch(`${baseURL}/details?page=${page}&size=${size}`);
  if (!response.ok) {
    throw new Error('Failed to fetch paged clients');
  }
  return await response.json();
};

export const fetchWildcardPage = (page: number): void => {
  // @ts-ignore: inputValue is not defined in this module
  const keyword = inputValue;
  fetch(`http://localhost:8089/search-integration/api/client-autocomplete?keyword=${encodeURIComponent(keyword)}`)
    .then((res) => res.json())
    .then((newData) => {
      // @ts-ignore: setClientList is not defined in this module
      setClientList(newData);
      // @ts-ignore: setCurrentPage is not defined in this module
      setCurrentPage(page);
    })
    .catch((error) => {
      console.error('Error fetching wildcard data:', error);
    });
};

export const fetchClientsByPage = async (page: number = 0, pageSize: number = 25): Promise<any> => {
  try {
    // const response = await fetch(`${baseURL}/clients-paging?page=${page}&size=${pageSize}`);
    const response = await fetch(`${baseURL}/details?page=${page}&size=${pageSize}`);
    if (!response.ok) {
      throw new Error('Failed to fetch clients');
    }
    const data = await response.json();
    return data; // Return the fetched data
  } catch (error) {
    console.error(`Error fetching clients for page ${page}:`, error);
    throw error;
  }
};

export const resetClientListService = async (pageSize: number = 25): Promise<any> => {
  try {
    // const response = await fetch(`${baseURL}/clients-paging?page=0&size=${pageSize}`);
    const response = await fetch(`${baseURL}/details?page=0&size=${pageSize}`);
    if (!response.ok) {
      throw new Error('Failed to fetch initial clients');
    }
    const data = await response.json();
    return data; // Return the reset data
  } catch (error) {
    console.error('Reset fetch failed:', error);
    throw error;
  }
};


/* ---------------- fetch utility ---------------- */
async function request(url: string, opts: RequestInit = {}): Promise<any> {
  const res = await fetch(url, opts);
  if (!res.ok) throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  return res.json();
}


export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  const encoded = encodeURIComponent(keyword.trim());
  const endpoint = keyword.trim().endsWith('*')
    ? `${baseURL}/client/wildcard?keyword=${encoded}`
    : `http://localhost:8089/search-integration/api/report-autocomplete?keyword=${encoded}`;
  return request(endpoint);
};
