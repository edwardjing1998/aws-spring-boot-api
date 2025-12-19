import React, { Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

// Lazy load the components
const MessageTable = React.lazy(() => import('./RapidAdmin/pages/RapidAdminEdit/MessageTable/MessageTable'));
const MailType = React.lazy(() => import('./RapidAdmin/pages/RapidAdminEdit/MailType/MailType'));
const EmailSetup = React.lazy(() => import('./RapidAdmin/pages/RapidAdminEdit/EmailSetup/EmailSetup'));

const AppRoutes: React.FC = () => (
  // Suspense is required when using React.lazy
  <Suspense fallback={<div>Loading...</div>}>
    <Routes>
      <Route path="admin/edit/messagetable" element={<MessageTable />} />
      <Route path="admin/edit/mailtype" element={<MailType />} />
      <Route path="admin/edit/emailsetup" element={<EmailSetup />} />
    </Routes>
  </Suspense>
);

export default AppRoutes;
