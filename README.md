Argument of type '(p: any) => any' is not assignable to parameter of type 'number'.

JSX elements cannot have multiple attributes with the same name.ts(17001)
(property) onClick?: React.MouseEventHandler<HTMLButtonElement> | undefined



        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setClientPage(Math.min(p => p + 1, pageCount - 1))}
          // Using conditional logic for safety: if we are at last page, disable.
          // Note: setClientPage accepts number, so we calculate new page directly
          onClick={() => setClientPage(Math.min(page + 1, pageCount - 1))}
          disabled={page >= pageCount - 1}
        >
          Next ▶
        </Button>
