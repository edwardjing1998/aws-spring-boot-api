import React, { Suspense } from 'react'
import { Navigate, Route, Routes } from 'react-router-dom'
import { CContainer, CSpinner } from '@coreui/react'

// routes config
// Assuming routes are exported from a file that might be .js or .ts
// You might need to rename client-routes.js to .ts or add a .d.ts file if it's JS
import routes from '../client-routes'

interface RouteItem {
  path: string;
  exact?: boolean;
  name?: string;
  element: React.ComponentType<any>;
}

const AppContent: React.FC = () => {
  return (
    <CContainer className="px-4" lg style={{ marginTop: '35px' }}>
      <Suspense fallback={<CSpinner color="primary" />}>
        <Routes>
          {(routes as RouteItem[]).map((route, idx) => {
            return (
              route.element && (
                <Route
                  key={idx}
                  path={route.path}
                  // exact property is not used in React Router v6, but kept if you rely on it elsewhere
                  // exact={route.exact} 
                  element={<route.element />}
                />
              )
            )
          })}
          <Route path="/" element={<Navigate to="dashboard" replace />} />
        </Routes>
      </Suspense>
    </CContainer>
  )
}

export default React.memo(AppContent)
