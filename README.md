import React, { useEffect } from 'react'
import { Route, Routes } from 'react-router-dom'
import { useSelector } from 'react-redux'
import { useColorModes } from '@coreui/react'

// Import the sub-router from your module folder
import ClientInfoRoutes from './modules/edit/client-information/ClientInfoRoutes'

type RootState = {
  theme: string
}

const App: React.FC = () => {
  const { isColorModeSet, setColorMode } = useColorModes(
    'coreui-free-react-admin-template-theme',
  )
  // Assuming Redux store structure for theme
  const storedTheme = useSelector<RootState, string>((state) => state.theme)

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.href.split('?')[1] ?? '')
    const theme = urlParams.get('theme')?.match(/^[A-Za-z0-9\s]+/i)?.[0]
    if (theme) setColorMode(theme)

    if (!isColorModeSet()) {
      setColorMode(storedTheme)
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [])

  return (
    <Routes>
      {/* Route traffic to the ClientInfoRoutes sub-router.
         The "*" wildcard ensures that nested paths (e.g., /dashboard) 
         are passed through to ClientInfoRoutes to handle.
      */}
      <Route path="/*" element={<ClientInfoRoutes />} />
    </Routes>
  )
}

export default App
