curl -X 'DELETE' \
  'http://localhost:8089/client-sysprin-writer/api/prefix/delete' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "billingSp": "22982000",
  "prefix": "9118120",
  "atmCashRule": "0"
}'
