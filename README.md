import React from 'react'
import { NavLink } from 'react-router-dom'
import SimpleBar from 'simplebar-react'
import 'simplebar-react/dist/simplebar.min.css'
import '../scss/navigation.scss'

interface NavItem {
  component: React.ElementType
  name?: string
  icon?: React.ReactNode
  badge?: {
    color: string
    text: string
  }
  to?: string
  href?: string
  items?: NavItem[]
  [key: string]: any
}

interface AppSidebarNavProps {
  items: NavItem[]
}

/**
 * Keep Bootstrap badge colors the same behavior as CoreUI (no new CSS).
 * CoreUI badge "color" maps well to bootstrap bg-*.
 */
function badgeClass(color?: string) {
  switch (color) {
    case 'primary':
      return 'bg-primary'
    case 'secondary':
      return 'bg-secondary'
    case 'success':
      return 'bg-success'
    case 'danger':
      return 'bg-danger'
    case 'warning':
      return 'bg-warning text-dark'
    case 'info':
      return 'bg-info text-dark'
    case 'light':
      return 'bg-light text-dark'
    case 'dark':
      return 'bg-dark'
    default:
      return 'bg-secondary'
  }
}

export const AppSidebarNav: React.FC<AppSidebarNavProps> = ({ items }) => {
  const navLink = (
    name: string | undefined,
    icon: React.ReactNode,
    badge: { color: string; text: string } | undefined,
    indent: boolean = false,
  ) => {
    return (
      <>
        {icon
          ? icon
          : indent && (
              <span className="nav-icon">
                <span className="nav-icon-bullet"></span>
              </span>
            )}
        {name && name}
        {badge && (
          // replace <CBadge> with plain span using the same bootstrap classes
          <span className={`ms-auto badge ${badgeClass(badge.color)}`} style={{ fontSize: '0.75em' }}>
            {badge.text}
          </span>
        )}
      </>
    )
  }

  const navItem = (item: NavItem, index: number, indent: boolean = false) => {
    const { component, name, badge, icon, ...rest } = item
    const Component = component

    // Keep original CoreUI behavior:
    // - if "to" use router link
    // - if "href" use external link
    // - otherwise render label only
    const content = rest.to ? (
      <NavLink to={rest.to} className={({ isActive }) => `nav-link ${isActive ? 'active' : ''}`}>
        {navLink(name, icon, badge, indent)}
      </NavLink>
    ) : rest.href ? (
      <a className="nav-link" href={rest.href} target="_blank" rel="noopener noreferrer">
        {navLink(name, icon, badge, indent)}
      </a>
    ) : (
      <div className="nav-link">{navLink(name, icon, badge, indent)}</div>
    )

    // Keep your <Component as="div"> structure without relying on CoreUI "as" prop.
    // Most CoreUI nav components render fine if used as a wrapper element type.
    return (
      <Component key={index}>
        {content}
      </Component>
    )
  }

  const navGroup = (item: NavItem, index: number) => {
    const { component, name, icon, items: childItems } = item
    const Component = component

    // CoreUI's "toggler" / "compact" props were CoreUI-specific.
    // We keep markup/classes consistent and rely on your navigation.scss.
    return (
      <Component key={index} className="nav-group show">
        <div className="nav-link nav-group-toggle">
          {navLink(name, icon, item.badge)}
        </div>

        <div className="nav-group-items">
          {childItems?.map((child, childIdx) =>
            child.items ? navGroup(child, childIdx) : navItem(child, childIdx, true),
          )}
        </div>
      </Component>
    )
  }

  return (
    // replace <CSidebarNav as={SimpleBar}> with SimpleBar wrapping a plain nav container
    <div className="sidebar-nav">
      <SimpleBar className="sidebar-scroll-hidden">
        {items &&
          items.map((item, index) => (item.items ? navGroup(item, index) : navItem(item, index)))}
      </SimpleBar>
    </div>
  )
}
