import { fetchPreviewAtmCashPrefixes } from './utils/PreviewAtmAndCashPrefixes.service';


    const fetchData = async () => {
      // 3. Call the service function
      const result = await fetchPreviewAtmCashPrefixes(clientId, page, PAGE_SIZE);
      setPrefixes(result);
    };
