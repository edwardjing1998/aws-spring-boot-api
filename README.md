import React, { Suspense } from 'react'
import { Route, Routes } from 'react-router-dom'
import { CSpinner } from '@coreui/react'

// Import styles relative to this folder
import './scss/style.scss'

// Containers (Lazy loaded relative to this folder)
const ClientSysPrinComponent = React.lazy(() => import('./ClientSysPrinComponent'))

const ClientInfoRoutes: React.FC = () => {
  return (
    <Suspense
      fallback={
        <div className="pt-3 text-center">
          <CSpinner color="primary" variant="grow" />
        </div>
      }
    >
      <Routes>
        {/* This path matches relative to where the parent router mounted it. 
            If parent mounts at "/*", this "*" captures everything. */}
        <Route path="*" element={<ClientSysPrinComponent />} />
      </Routes>
    </Suspense>
  )
}

export default ClientInfoRoutes
