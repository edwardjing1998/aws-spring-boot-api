import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { AppSidebarNav } from './AppSidebarNav'

// sidebar nav config
import defaultNav from '../_nav'

const AppSidebar: React.FC = () => {
  const dispatch = useDispatch()
  const unfoldable = useSelector((state: any) => state.sidebarUnfoldable)
  const sidebarShow = useSelector((state: any) => state.sidebarShow)

  return (
    <aside
      className={[
        'sidebar',
        'sidebar-fixed',
        'sidebar-light',
        unfoldable ? 'sidebar-narrow-unfoldable' : '',
        sidebarShow ? 'show' : '',
      ]
        .filter(Boolean)
        .join(' ')}
      style={{ position: 'fixed' }}
    >
      {/* Header */}
      <div className="sidebar-header">
        <button
          type="button"
          className="btn-close d-lg-none"
          aria-label="Close"
          onClick={() => dispatch({ type: 'set', sidebarShow: false })}
        />
      </div>

      {/* Nav */}
      <div className="sidebar-nav">
        {/* keep same API, but relax typing to avoid TS2322 caused by CoreUI nav config shape */}
        <AppSidebarNav items={defaultNav as any} />
      </div>

      {/* Footer */}
      <div className="sidebar-footer border-top d-none d-lg-flex">
        <button
          type="button"
          className="sidebar-toggler"
          aria-label="Toggle sidebar"
          onClick={() => dispatch({ type: 'set', sidebarUnfoldable: !unfoldable })}
        />
      </div>
    </aside>
  )
}

export default React.memo(AppSidebar)
