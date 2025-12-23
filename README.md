import React from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { NavLink } from 'react-router-dom'

// ✅ Use your route list instead of CoreUI _nav to avoid NavItem typing problems
// If your routes export structure differs, adjust accordingly.
import routes from '../client-routes'

type RootState = any

const SIDEBAR_WIDTH = 256
const SIDEBAR_WIDTH_COLLAPSED = 72

const AppSidebar: React.FC = () => {
  const dispatch = useDispatch()
  const unfoldable = useSelector((state: RootState) => state.sidebarUnfoldable)
  const sidebarShow = useSelector((state: RootState) => state.sidebarShow)

  const width = unfoldable ? SIDEBAR_WIDTH_COLLAPSED : SIDEBAR_WIDTH

  // Basic filtering: show only routes with name + path (customize as you like)
  const navItems = (routes as any[])
    .filter((r) => r && typeof r.path === 'string' && r.name)
    .filter((r) => !r.path.includes(':')) // optional: skip param routes

  return (
    <>
      {/* Backdrop for mobile / when sidebar is open */}
      {sidebarShow && (
        <div
          onClick={() => dispatch({ type: 'set', sidebarShow: false })}
          style={{
            position: 'fixed',
            inset: 0,
            background: 'rgba(0,0,0,0.35)',
            zIndex: 1090,
          }}
        />
      )}

      <aside
        aria-label="Sidebar"
        style={{
          position: 'fixed',
          top: 0,
          left: 0,
          height: '100vh',
          width,
          background: '#ffffff',
          borderRight: '1px solid #e5e7eb',
          zIndex: 1100,
          transform: sidebarShow ? 'translateX(0)' : 'translateX(-105%)',
          transition: 'transform 180ms ease, width 180ms ease',
          display: 'flex',
          flexDirection: 'column',
        }}
      >
        {/* Header */}
        <div
          className="d-flex align-items-center justify-content-between px-3"
          style={{ height: 56, borderBottom: '1px solid #e5e7eb' }}
        >
          <div style={{ fontWeight: 600, fontSize: 14, whiteSpace: 'nowrap', overflow: 'hidden' }}>
            {unfoldable ? 'RA' : 'Rapid Admin'}
          </div>

          {/* Close button (mobile) */}
          <button
            type="button"
            className="btn btn-sm btn-light d-lg-none"
            onClick={() => dispatch({ type: 'set', sidebarShow: false })}
            aria-label="Close sidebar"
          >
            ✕
          </button>
        </div>

        {/* Nav */}
        <nav style={{ flex: 1, overflowY: 'auto', padding: 8 }}>
          <ul className="list-unstyled m-0">
            {navItems.map((item, idx) => (
              <li key={`${item.path}-${idx}`} style={{ marginBottom: 4 }}>
                <NavLink
                  to={item.path}
                  className={({ isActive }) =>
                    [
                      'd-flex align-items-center',
                      'text-decoration-none',
                      'px-2 py-2',
                      'rounded',
                      isActive ? 'bg-primary text-white' : 'text-dark',
                    ].join(' ')
                  }
                  style={{
                    fontSize: 13,
                    gap: 10,
                  }}
                  onClick={() => {
                    // optional: auto close on mobile
                    if (window.innerWidth < 992) dispatch({ type: 'set', sidebarShow: false })
                  }}
                >
                  {/* Simple dot icon placeholder */}
                  <span
                    aria-hidden
                    style={{
                      width: 10,
                      height: 10,
                      borderRadius: 999,
                      background: 'currentColor',
                      opacity: 0.65,
                      flex: '0 0 auto',
                    }}
                  />
                  <span
                    style={{
                      whiteSpace: 'nowrap',
                      overflow: 'hidden',
                      textOverflow: 'ellipsis',
                      display: unfoldable ? 'none' : 'block',
                    }}
                  >
                    {item.name}
                  </span>
                </NavLink>
              </li>
            ))}
          </ul>
        </nav>

        {/* Footer */}
        <div
          className="d-none d-lg-flex align-items-center justify-content-between px-3"
          style={{ height: 52, borderTop: '1px solid #e5e7eb' }}
        >
          <button
            type="button"
            className="btn btn-sm btn-outline-primary"
            onClick={() => dispatch({ type: 'set', sidebarUnfoldable: !unfoldable })}
          >
            {unfoldable ? 'Expand' : 'Collapse'}
          </button>
        </div>
      </aside>
    </>
  )
}

export default React.memo(AppSidebar)























ERROR in src/Client/layout/AppSidebarNav.tsx:45:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    43 |         {name && name}
    44 |         {badge && (
  > 45 |           <CBadge color={badge.color} className="ms-auto" size="sm">
       |            ^^^^^^
    46 |             {badge.text}
    47 |           </CBadge>
    48 |         )}

ERROR in src/Client/layout/AppSidebarNav.tsx:59:12
TS2786: 'CNavLink' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    57 |       <Component as="div" key={index}>
    58 |         {rest.to || rest.href ? (
  > 59 |           <CNavLink
       |            ^^^^^^^^
    60 |             {...(rest.to && { as: NavLink })}
    61 |             {...(rest.href && { target: '_blank', rel: 'noopener noreferrer' })}
    62 |             {...rest}

ERROR in src/Client/layout/AppSidebarNav.tsx:86:6
TS2786: 'CSidebarNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    84 |
    85 |   return (
  > 86 |     <CSidebarNav as={SimpleBar} className="sidebar-scroll-hidden">
       |      ^^^^^^^^^^^
    87 |       {items &&
    88 |         items.map((item, index) => (item.items ? navGroup(item, index) : navItem(item, index)))}
    89 |     </CSidebarNav>

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
