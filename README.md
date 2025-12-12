import { ClientReportItem } from '../EditClientReport.types';

/**
 * Fetches the paginated report options for a specific client.
 */
export const fetchPreviewClientReports = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<ClientReportItem[]> => {
  try {
    const url = `http://localhost:8089/client-sysprin-reader/api/client/report-option/${encodeURIComponent(clientId)}?page=${page}&size=${pageSize}`;

    const resp = await fetch(url, {
      method: 'GET',
      headers: { accept: '*/*' },
    });

    if (resp.ok) {
      const json: ClientReportItem[] = await resp.json();
      return json;
    } else {
      console.error("Failed to fetch reports");
      return [];
    }
  } catch (error) {
    console.error("Error fetching reports:", error);
    return [];
  }
};
