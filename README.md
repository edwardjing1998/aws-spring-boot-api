// src/AppRoutes.tsx
// ... imports ...

const AppRoutes: React.FC = () => {
  return (
    <Suspense fallback={<CSpinner />}>
      <Routes>
        {/* These paths are relative to "/admin" */}
        <Route path="/dashboard" element={<Dashboard />} /> 
        <Route path="/edit/client-information" element={<ClientInformationPanel />} />
        {/* ... other admin routes ... */}
      </Routes>
    </Suspense>
  )
}
export default AppRoutes;
