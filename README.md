SELECT 
    t.name AS TableName,
    c.name AS ColumnName
FROM 
    sys.columns c
JOIN 
    sys.tables t ON c.object_id = t.object_id
WHERE 
    c.name = 'sys_prin'
ORDER BY 
    t.name;
