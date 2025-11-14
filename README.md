// put this inside your component, before the return
const getPageButtonStyle = (isActive) => ({
  marginRight: 8,
  padding: '3px 10px',
  fontSize: '0.78rem',          // smaller font size
  borderRadius: 12,
  border: '1px solid #8ecae6',  // light blue border
  backgroundColor: isActive ? '#BEEBE2' : '#ffffff', // active / inactive
  color: '#000',
  cursor: isActive ? 'default' : 'pointer',
  minWidth: 32,
});



<div style={{ display: 'flex', justifyContent: 'center', marginBottom: 12 }}>
  <button
    onClick={() => setPage(1)}
    disabled={page === 1}
    style={getPageButtonStyle(page === 1)}
  >
    1
  </button>
  <button
    onClick={() => setPage(2)}
    disabled={page === 2}
    style={getPageButtonStyle(page === 2)}
  >
    2
  </button>
</div>
