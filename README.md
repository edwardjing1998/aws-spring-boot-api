import React, { useMemo, useState } from 'react'
import { NavLink, useLocation } from 'react-router-dom'
import SimpleBar from 'simplebar-react'
import 'simplebar-react/dist/simplebar.min.css'
import '../scss/navigation.scss'

interface NavItem {
  component?: React.ElementType
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

// keep bootstrap badge colors without injecting new CSS
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
  const location = useLocation()

  // keep open state, but don’t enforce “open by default” visually via new CSS
  const [openGroups, setOpenGroups] = useState<Record<string, boolean>>({})

  const toggleGroup = (key: string) => {
    setOpenGroups((prev) => ({ ...prev, [key]: !(prev[key] ?? true) }))
  }

  const renderBadge = (badge?: { color: string; text: string }) => {
    if (!badge) return null
    return (
      <span className={`badge ms-auto ${badgeClass(badge.color)}`}>
        {badge.text}
      </span>
    )
  }

  const renderLinkContent = (
    name?: string,
    icon?: React.ReactNode,
    badge?: { color: string; text: string },
    indent?: boolean,
  ) => {
    return (
      <>
        {icon ? (
          icon
        ) : indent ? (
          <span className="nav-icon">
            <span className="nav-icon-bullet" />
          </span>
        ) : null}

        {name}
        {renderBadge(badge)}
      </>
    )
  }

  const isGroupActive = (group: NavItem): boolean => {
    // open group if any child matches current route
    const walk = (node: NavItem): boolean => {
      if (node.to && location.pathname.startsWith(node.to)) return true
      if (!node.items?.length) return false
      return node.items.some(walk)
    }
    return walk(group)
  }

  const renderItem = (item: NavItem, keyPath: string, indent = false) => {
    const { name, icon, badge, to, href } = item

    if (href) {
      return (
        <li key={keyPath} className="nav-item">
          <a
            className="nav-link"
            href={href}
            target="_blank"
            rel="noopener noreferrer"
          >
            {renderLinkContent(name, icon, badge, indent)}
          </a>
        </li>
      )
    }

    if (to) {
      return (
        <li key={keyPath} className="nav-item">
          <NavLink
            to={to}
            className={({ isActive }) => `nav-link ${isActive ? 'active' : ''}`}
          >
            {renderLinkContent(name, icon, badge, indent)}
          </NavLink>
        </li>
      )
    }

    return (
      <li key={keyPath} className="nav-item">
        <div className="nav-link">
          {renderLinkContent(name, icon, badge, indent)}
        </div>
      </li>
    )
  }

  const renderGroup = (item: NavItem, keyPath: string) => {
    const { name, icon, badge, items: children } = item

    // keep behavior similar to CoreUI: open group if active, else use state, default open
    const active = isGroupActive(item)
    const isOpen = active || (openGroups[keyPath] ?? true)

    return (
      <li key={keyPath} className={`nav-group ${isOpen ? 'show' : ''}`}>
        <button
          type="button"
          className={`nav-link nav-group-toggle ${active ? 'active' : ''}`}
          onClick={() => toggleGroup(keyPath)}
        >
          {renderLinkContent(name, icon, badge, false)}
        </button>

        {isOpen && children?.length ? (
          <ul className="nav-group-items list-unstyled">
            {children.map((child, idx) => {
              const childKey = `${keyPath}.${idx}`
              return child.items
                ? renderGroup(child, childKey)
                : renderItem(child, childKey, true)
            })}
          </ul>
        ) : null}
      </li>
    )
  }

  const rendered = useMemo(() => {
    return (
      <ul className="nav list-unstyled">
        {items?.map((item, idx) => {
          const keyPath = `root.${idx}`
          return item.items ? renderGroup(item, keyPath) : renderItem(item, keyPath, false)
        })}
      </ul>
    )
  }, [items, openGroups, location.pathname])

  return (
    <SimpleBar style={{ maxHeight: '100%' }}>
      {rendered}
    </SimpleBar>
  )
}
