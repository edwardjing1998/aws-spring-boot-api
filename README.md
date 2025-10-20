-- Update the row with the smallest ID among the duplicates
UPDATE t
SET    target_col = 'new value'
FROM   dbo.YourTable AS t
WHERE  t.Id = (
  SELECT MIN(Id) 
  FROM dbo.YourTable 
  WHERE colA = @a AND colB = @b
);
