<EditAtmCashPrefix
                      selectedGroupRow={viewRow}
                      onDataChange={(nextSelectedGroupRow) => {
                        setSelectedGroupRow((prev) => {
                          if (!prev) return nextSelectedGroupRow;
                          const a = prev?.sysPrinsPrefixes ?? [];
                          const b = nextSelectedGroupRow?.sysPrinsPrefixes ?? [];
                          if (equalPrefixes(a, b)) return prev; // no-op
                          const merged = { ...prev, sysPrinsPrefixes: b };
                          onClientUpdated?.(merged);
                          return merged;
                        });
                      }}
                    />
