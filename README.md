import React from 'react'
import { useLocation, Navigate } from 'react-router-dom'
import { AppContent, AppSidebar, AppFooter, AppHeader } from './layout/index'

const ClientSysPrinComponent: React.FC = () => {

  const location = useLocation()

  // Redirect to ArchiveDashboard if the archive system path is active
  if (location.pathname === '/navigation/archiveSystem') {
    return <Navigate to="/archive-dashboard" replace />
  }

  return (
    <div>
      <AppSidebar />
      <div className="wrapper d-flex flex-column min-vh-100">
        <AppHeader />
        <div className="body flex-grow-1">
          <AppContent />
        </div>
        <AppFooter />
      </div>
    </div>
  )
}

export default ClientSysPrinComponent
