 // const baseURL = 'http://localhost:4444/api';
 const baseURL = 'http://localhost:8089/client-sysprin-reader/api/client';
                  


export const fetchClientsPaging = async (page, size) => {
 //   const response = await fetch(`${baseURL}/clients-paging?page=${page}&size=${size}`);
 const response = await fetch(`${baseURL}/details?page=${page}&size=${size}`);
    if (!response.ok) {
      throw new Error('Failed to fetch paged clients');
    }
    return await response.json();
  };
  
export const fetchWildcardPage = (page) => {
const keyword = inputValue;
fetch(`http://localhost:8089/search-integration/api/client-autocomplete?keyword=${encodeURIComponent(keyword)}`)
    .then((res) => res.json())
    .then((newData) => {
    setClientList(newData);
    setCurrentPage(page);
    })
    .catch((error) => {
    console.error('Error fetching wildcard data:', error);
    });
};

export const fetchClientsByPage = async (page = 0, pageSize = 25) => {
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
  
  export const resetClientListService = async (pageSize = 25) => {
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
async function request(url, opts = {}) {
  const res = await fetch(url, opts);
  if (!res.ok) throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  return res.json();
}


export const fetchClientSuggestions = async (keyword) => {
  const encoded = encodeURIComponent(keyword.trim());
  const endpoint = keyword.trim().endsWith('*')
    ? `${baseURL}/client/wildcard?keyword=${encoded}`
    : `http://localhost:8089/search-integration/api/client-autocomplete?keyword=${encoded}`;
  return request(endpoint);
};
