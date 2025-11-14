import React, { useState } from 'react';

// ---- Page 1 ----
const ReviewPageOne = ({ formData, setFormData }) => {
  return (
    <div style={{ padding: 16 }}>
      <h3>Page 1 - Review General Info</h3>

      <div style={{ marginBottom: 8 }}>
        <label style={{ display: 'block', fontSize: '0.85rem' }}>Client ID</label>
        <input
          type="text"
          value={formData.client || ''}
          onChange={(e) => setFormData(prev => ({ ...prev, client: e.target.value }))}
        />
      </div>

      <div style={{ marginBottom: 8 }}>
        <label style={{ display: 'block', fontSize: '0.85rem' }}>Client Name</label>
        <input
          type="text"
          value={formData.name || ''}
          onChange={(e) => setFormData(prev => ({ ...prev, name: e.target.value }))}
        />
      </div>
    </div>
  );
};

// ---- Page 2 ----
const ReviewPageTwo = ({ formData, setFormData }) => {
  return (
    <div style={{ padding: 16 }}>
      <h3>Page 2 - Review Status / Submit</h3>

      <div style={{ marginBottom: 8 }}>
        <label style={{ display: 'block', fontSize: '0.85rem' }}>Status</label>
        <select
          value={formData.status || ''}
          onChange={(e) => setFormData(prev => ({ ...prev, status: e.target.value }))}
        >
          <option value="">-- Select --</option>
          <option value="ACTIVE">Active</option>
          <option value="INACTIVE">Inactive</option>
        </select>
      </div>

      <div style={{ marginTop: 16, padding: 12, border: '1px solid #ccc', fontSize: '0.85rem' }}>
        <b>Summary:</b>
        <div>Client: {formData.client || '(none)'}</div>
        <div>Name: {formData.name || '(none)'}</div>
        <div>Status: {formData.status || '(none)'}</div>
      </div>
    </div>
  );
};

// ---- Parent with pagination for 2 pages ----
const TwoPagePagination = () => {
  const [page, setPage] = useState(1);   // 1 or 2
  const [formData, setFormData] = useState({
    client: '',
    name: '',
    status: '',
  });

  const goNext = () => setPage((p) => Math.min(p + 1, 2));
  const goPrev = () => setPage((p) => Math.max(p - 1, 1));

  const handleSubmit = () => {
    // here you call your REST API to submit to DB
    console.log('Submitting payload:', formData);
    alert('Submitted! Check console for payload.');
  };

  return (
    <div style={{ border: '1px solid #ddd', borderRadius: 8, padding: 16, width: 600 }}>
      {/* Simple "pagination" header */}
      <div style={{ display: 'flex', justifyContent: 'center', marginBottom: 12 }}>
        <button
          onClick={() => setPage(1)}
          disabled={page === 1}
          style={{ marginRight: 8 }}
        >
          1
        </button>
        <button
          onClick={() => setPage(2)}
          disabled={page === 2}
        >
          2
        </button>
      </div>

      {/* Render current page */}
      {page === 1 && (
        <ReviewPageOne formData={formData} setFormData={setFormData} />
      )}
      {page === 2 && (
        <ReviewPageTwo formData={formData} setFormData={setFormData} />
      )}

      {/* Footer buttons */}
      <div
        style={{
          marginTop: 16,
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
        }}
      >
        <button onClick={goPrev} disabled={page === 1}>
          Back
        </button>

        <div>
          {page === 1 && (
            <button onClick={goNext}>
              Next
            </button>
          )}

          {page === 2 && (
            <button onClick={handleSubmit}>
              Submit
            </button>
          )}
        </div>
      </div>
    </div>
  );
};

export default TwoPagePagination;
