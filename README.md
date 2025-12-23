import React, { useMemo, useState } from 'react'
import { NavLink } from 'react-router-dom'
import SimpleBar from 'simplebar-react'
import 'simplebar-react/dist/simplebar.min.css'
import '../scss/navigation.scss'

interface NavItem {
  component?: React.ElementType // kept for backward compatibility, but we won't use it
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
 * Simple bootstrap-like badge color mapping.
 * Adjust classes to match your CSS.
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
  // track open/close of groups by "key path"
  const [openGroups, setOpenGroups] = useState<Record<string, boolean>>({})

  const toggleGroup = (key: string) => {
    setOpenGroups((prev) => ({ ...prev, [key]: !prev[key] }))
  }

  const renderBadge = (badge?: { color: string; text: string }) => {
    if (!badge) return null
    return (
      <span
        className={`ms-auto badge ${badgeClass(badge.color)}`}
        style={{ fontSize: '0.70rem', fontWeight: 600, padding: '0.25em 0.5em' }}
      >
        {badge.text}
      </span>
    )
  }

  const renderLabel = (
    name?: string,
    icon?: React.ReactNode,
    badge?: { color: string; text: string },
    indent?: boolean,
  ) => {
    return (
      <>
        {icon ? (
          <span className="nav-icon">{icon}</span>
        ) : indent ? (
          <span className="nav-icon">
            <span className="nav-icon-bullet" />
          </span>
        ) : null}

        <span className="nav-label">{name ?? ''}</span>

        {renderBadge(badge)}
      </>
    )
  }

  const renderItem = (item: NavItem, keyPath: string, indent = false) => {
    const { name, icon, badge, to, href } = item

    // external link
    if (href) {
      return (
        <li key={keyPath} className={`sidebar-item ${indent ? 'is-indent' : ''}`}>
          <a
            className="sidebar-link"
            href={href}
            target="_blank"
            rel="noopener noreferrer"
          >
            {renderLabel(name, icon, badge, indent)}
          </a>
        </li>
      )
    }

    // router link
    if (to) {
      return (
        <li key={keyPath} className={`sidebar-item ${indent ? 'is-indent' : ''}`}>
          <NavLink
            to={to}
            className={({ isActive }) =>
              `sidebar-link ${isActive ? 'is-active' : ''}`
            }
          >
            {renderLabel(name, icon, badge, indent)}
          </NavLink>
        </li>
      )
    }

    // plain label (non-clickable)
    return (
      <li key={keyPath} className={`sidebar-item ${indent ? 'is-indent' : ''}`}>
        <div className="sidebar-link is-static">
          {renderLabel(name, icon, badge, indent)}
        </div>
      </li>
    )
  }

  const renderGroup = (item: NavItem, keyPath: string) => {
    const { name, icon, badge, items: children } = item
    const isOpen = openGroups[keyPath] ?? true // default open; change to false if you want collapsed initially

    return (
      <li key={keyPath} className="sidebar-group">
        <button
          type="button"
          className={`sidebar-group-toggle ${isOpen ? 'is-open' : ''}`}
          onClick={() => toggleGroup(keyPath)}
        >
          <span className="sidebar-group-toggle-left">
            {renderLabel(name, icon, badge, false)}
          </span>
          <span className="sidebar-group-caret" aria-hidden>
            ▾
          </span>
        </button>

        {isOpen && children?.length ? (
          <ul className="sidebar-group-list list-unstyled m-0">
            {children.map((child, idx) => {
              const childKey = `${keyPath}.${idx}`
              return child.items ? renderGroup(child, childKey) : renderItem(child, childKey, true)
            })}
          </ul>
        ) : null}
      </li>
    )
  }

  const rendered = useMemo(() => {
    return (
      <ul className="sidebar-nav list-unstyled m-0">
        {items?.map((item, idx) => {
          const keyPath = `root.${idx}`
          return item.items ? renderGroup(item, keyPath) : renderItem(item, keyPath, false)
        })}
      </ul>
    )
  }, [items, openGroups]) // openGroups affects group rendering

  return (
    <div className="sidebar-scroll-hidden">
      <SimpleBar style={{ maxHeight: '100%' }}>{rendered}</SimpleBar>
    </div>
  )
}



























ERROR in src/Client/layout/CustomSnackbar.tsx:60:34
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    Type 'undefined' is not assignable to type 'Element | null'.
    58 |                         {type === 'delete' ? (
    59 |                             <>
  > 60 |                                 <CButton className='cancel-button button-text' onClick={(e) => onClose(e)}>Cancel</CButton>
       |                                  ^^^^^^^
    61 |                                 <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
    62 |                             </>
    63 |                         ) : (

ERROR in src/Client/layout/CustomSnackbar.tsx:61:34
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    59 |                             <>
    60 |                                 <CButton className='cancel-button button-text' onClick={(e) => onClose(e)}>Cancel</CButton>
  > 61 |                                 <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
       |                                  ^^^^^^^
    62 |                             </>
    63 |                         ) : (
    64 |                             <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>

ERROR in src/Client/layout/CustomSnackbar.tsx:64:30
TS2786: 'CButton' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    62 |                             </>
    63 |                         ) : (
  > 64 |                             <CButton className='ok-button button-text' onClick={handleOk}>OK</CButton>
       |                              ^^^^^^^
    65 |                         )}
    66 |                     </div>
    67 |                 </>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:30:6
TS2786: 'CDropdown' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    Type 'undefined' is not assignable to type 'Element | null'.
    28 | const AppHeaderDropdown: React.FC = () => {
    29 |   return (
  > 30 |     <CDropdown variant="nav-item">
       |      ^^^^^^^^^
    31 |       <CDropdownToggle placement="bottom-end" className="py-0 pe-0" caret={false}>
    32 |         <CAvatar src={avatar8} size="md" />
    33 |       </CDropdownToggle>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:31:24
TS2322: Type '{ children: Element; placement: string; className: string; caret: false; }' is not assignable to type 'IntrinsicAttributes & CDropdownToggleProps'.
  Property 'placement' does not exist on type 'IntrinsicAttributes & CDropdownToggleProps'.
    29 |   return (
    30 |     <CDropdown variant="nav-item">
  > 31 |       <CDropdownToggle placement="bottom-end" className="py-0 pe-0" caret={false}>
       |                        ^^^^^^^^^
    32 |         <CAvatar src={avatar8} size="md" />
    33 |       </CDropdownToggle>
    34 |       <CDropdownMenu className="pt-0" placement="bottom-end">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:34:8
TS2786: 'CDropdownMenu' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    32 |         <CAvatar src={avatar8} size="md" />
    33 |       </CDropdownToggle>
  > 34 |       <CDropdownMenu className="pt-0" placement="bottom-end">
       |        ^^^^^^^^^^^^^
    35 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
    36 |         <CDropdownItem href="#">
    37 |           <CIcon icon={cilBell} className="me-2" />

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:34:39
TS2322: Type '{ children: Element[]; className: string; placement: string; }' is not assignable to type 'IntrinsicAttributes & Omit<DetailedHTMLProps<HTMLAttributes<HTMLUListElement>, HTMLUListElement>, Props<...> & CDropdownMenuProps> & Props<...> & CDropdownMenuProps & { ...; }'.
  Property 'placement' does not exist on type 'IntrinsicAttributes & Omit<DetailedHTMLProps<HTMLAttributes<HTMLUListElement>, HTMLUListElement>, Props<...> & CDropdownMenuProps> & Props<...> & CDropdownMenuProps & { ...; }'.
    32 |         <CAvatar src={avatar8} size="md" />
    33 |       </CDropdownToggle>
  > 34 |       <CDropdownMenu className="pt-0" placement="bottom-end">
       |                                       ^^^^^^^^^
    35 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
    36 |         <CDropdownItem href="#">
    37 |           <CIcon icon={cilBell} className="me-2" />

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:35:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    33 |       </CDropdownToggle>
    34 |       <CDropdownMenu className="pt-0" placement="bottom-end">
  > 35 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
       |          ^^^^^^^^^^^^^^^
    36 |         <CDropdownItem href="#">
    37 |           <CIcon icon={cilBell} className="me-2" />
    38 |           Updates

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:36:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    34 |       <CDropdownMenu className="pt-0" placement="bottom-end">
    35 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
  > 36 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    37 |           <CIcon icon={cilBell} className="me-2" />
    38 |           Updates
    39 |           <CBadge color="info" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:39:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    37 |           <CIcon icon={cilBell} className="me-2" />
    38 |           Updates
  > 39 |           <CBadge color="info" className="ms-2">
       |            ^^^^^^
    40 |             42
    41 |           </CBadge>
    42 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:43:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    41 |           </CBadge>
    42 |         </CDropdownItem>
  > 43 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    44 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    45 |           Messages
    46 |           <CBadge color="success" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:46:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    44 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    45 |           Messages
  > 46 |           <CBadge color="success" className="ms-2">
       |            ^^^^^^
    47 |             42
    48 |           </CBadge>
    49 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:50:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    48 |           </CBadge>
    49 |         </CDropdownItem>
  > 50 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    51 |           <CIcon icon={cilTask} className="me-2" />
    52 |           Tasks
    53 |           <CBadge color="danger" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:53:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    51 |           <CIcon icon={cilTask} className="me-2" />
    52 |           Tasks
  > 53 |           <CBadge color="danger" className="ms-2">
       |            ^^^^^^
    54 |             42
    55 |           </CBadge>
    56 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:57:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    55 |           </CBadge>
    56 |         </CDropdownItem>
  > 57 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    58 |           <CIcon icon={cilCommentSquare} className="me-2" />
    59 |           Comments
    60 |           <CBadge color="warning" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:60:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    58 |           <CIcon icon={cilCommentSquare} className="me-2" />
    59 |           Comments
  > 60 |           <CBadge color="warning" className="ms-2">
       |            ^^^^^^
    61 |             42
    62 |           </CBadge>
    63 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:64:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    62 |           </CBadge>
    63 |         </CDropdownItem>
  > 64 |         <CDropdownHeader className="bg-body-secondary fw-semibold my-2">Settings</CDropdownHeader>
       |          ^^^^^^^^^^^^^^^
    65 |         <CDropdownItem href="#">
    66 |           <CIcon icon={cilUser} className="me-2" />
    67 |           Profile

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:65:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    63 |         </CDropdownItem>
    64 |         <CDropdownHeader className="bg-body-secondary fw-semibold my-2">Settings</CDropdownHeader>
  > 65 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    66 |           <CIcon icon={cilUser} className="me-2" />
    67 |           Profile
    68 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:69:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    67 |           Profile
    68 |         </CDropdownItem>
  > 69 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    70 |           <CIcon icon={cilSettings} className="me-2" />
    71 |           Settings
    72 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:73:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    71 |           Settings
    72 |         </CDropdownItem>
  > 73 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    74 |           <CIcon icon={cilCreditCard} className="me-2" />
    75 |           Payments
    76 |           <CBadge color="secondary" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:76:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    74 |           <CIcon icon={cilCreditCard} className="me-2" />
    75 |           Payments
  > 76 |           <CBadge color="secondary" className="ms-2">
       |            ^^^^^^
    77 |             42
    78 |           </CBadge>
    79 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:80:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    78 |           </CBadge>
    79 |         </CDropdownItem>
  > 80 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    81 |           <CIcon icon={cilFile} className="me-2" />
    82 |           Projects
    83 |           <CBadge color="primary" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:83:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    81 |           <CIcon icon={cilFile} className="me-2" />
    82 |           Projects
  > 83 |           <CBadge color="primary" className="ms-2">
       |            ^^^^^^
    84 |             42
    85 |           </CBadge>
    86 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:88:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    86 |         </CDropdownItem>
    87 |         <CDropdownDivider />
  > 88 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    89 |           <CIcon icon={cilLockLocked} className="me-2" />
    90 |           Lock Account
    91 |         </CDropdownItem>
