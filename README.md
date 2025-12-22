import React, { useState, useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { useLocation } from 'react-router-dom'

import {
  CCloseButton,
  CSidebar,
  CSidebarBrand,
  CSidebarFooter,
  CSidebarHeader,
  CSidebarToggler,
  CSidebarNav,
} from '@coreui/react'

import { AppSidebarNav } from './AppSidebarNav'

// sidebar nav config
import defaultNav from '../_nav'
import archiveNav from '../_archiveNav'

const AppSidebar = () => {
  const dispatch = useDispatch()
  const unfoldable = useSelector((state) => state.sidebarUnfoldable)
  const sidebarShow = useSelector((state) => state.sidebarShow)

  const location = useLocation()

  const [navItems, setNavItems] = useState(defaultNav)

  useEffect(() => {
    // Define all routes (or route prefixes) where archiveNav should be used
    const archivePaths = [
      '/archive-dashboard',
      '/report/input-rebot-totals',
      '/archive-query-maintenance/define-query',
      '/archive-query-maintenance/c3-file-transfer',
      '/archive-query-maintenance/data-definitions',
      '/archive-query-maintenance/schedule-batch-report',
      '/archive-query-maintenance/table-load',
      '/archive-query-maintenance/table-load-column-mapping',
      '/archive-query-maintenance/tool-tips',
      '/archive-maintenance/client-report-mapping',
      '/archive-maintenance/resend-web-reports',
      '/archive-maintenance/web-client-directory',
      '/archive-report/billing',
      '/archive-maintenance/input-robot-totals',
      '/archive-report/unmatch-sys-prins',
      '/archive-report/report-queries',
      '/archive-report/email-event-id',
    ]

    const isArchiveRoute = archivePaths.some(path =>
      location.pathname === path || location.pathname.startsWith(path)
    )

    setNavItems(isArchiveRoute ? archiveNav : defaultNav)
  }, [location])

  return (
    <CSidebar
      colorScheme="light"
      position="fixed"
      unfoldable={unfoldable}
      visible={sidebarShow}
      onVisibleChange={(visible) => {
        dispatch({ type: 'set', sidebarShow: visible })
      }}
    >
      <CSidebarHeader>
        <CCloseButton
          className="d-lg-none"
          dark
          onClick={() => dispatch({ type: 'set', sidebarShow: false })}
        />
      </CSidebarHeader>

      <CSidebarNav>
        <AppSidebarNav items={navItems} />
      </CSidebarNav>

      <CSidebarFooter className="border-top d-none d-lg-flex">
        <CSidebarToggler
          onClick={() =>
            dispatch({ type: 'set', sidebarUnfoldable: !unfoldable })
          }
        />
      </CSidebarFooter>
    </CSidebar>
  )
}

export default React.memo(AppSidebar)
