import React from 'react';
import ReactCountryFlag from 'react-country-flag';

const USFlag = () => {
  return (
    <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
      <ReactCountryFlag
        countryCode="US"
        svg
        style={{
          width: '1.5em',
          height: '1.5em',
        }}
        title="United States"
      />
      <span style={{ fontSize: '0.9rem' }}>United States</span>
    </div>
  );
};

export default USFlag;
