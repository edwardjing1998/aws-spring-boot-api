// src/App.tsx
import React, { Suspense, useEffect } from 'react'
import { Route, Routes } from 'react-router-dom'
import { useSelector } from 'react-redux'

import { CSpinner, useColorModes } from '@coreui/react'
import './modules/edit/client-information/scss/style.scss'

// Containers
const ClientSysPrinComponent = React.lazy(() => import('./modules/edit/client-information/ClientSysPrinComponent'))

type RootState = {
  theme: string
}

const App: React.FC = () => {
  const { isColorModeSet, setColorMode } = useColorModes(
    'coreui-free-react-admin-template-theme',
  )
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
    <Suspense
      fallback={
        <div className="pt-3 text-center">
          <CSpinner color="primary" variant="grow" />
        </div>
      }
    >
      <Routes>
        <Route path="*" element={<ClientSysPrinComponent />} />
      </Routes>
    </Suspense>
  )
}

export default App
