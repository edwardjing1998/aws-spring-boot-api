<TableBody>
                  {selectedInvalidAreas.length === 0 ? (
                    <TableRow sx={{ '& td': compactCellSx }}>
                      <TableCell sx={{ ...compactCellSx, ...font78 }} colSpan={2}>
                        <em>No areas selected.</em>
                      </TableCell>
                    </TableRow>
                  ) : (
                    selectedInvalidAreas.map((name, idx) => (
                      <TableRow key={`${name}-${idx}`} sx={{ '& td': compactCellSx }}>
                        <TableCell sx={{ ...compactCellSx, ...font78 }}>{name}</TableCell>
                        <TableCell sx={{ ...compactCellSx }} align="right">
                          <IconButton
                            size="small"
                            aria-label={`Delete ${name}`}
                            onClick={() => handleDeleteArea(name)}
                            disabled={!isEditable}
                          >
                            <DeleteIcon fontSize="small" />
                          </IconButton>
                        </TableCell>
                      </TableRow>
                    ))
                  )}
                </TableBody>
