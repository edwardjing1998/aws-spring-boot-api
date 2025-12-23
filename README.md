import React from 'react'
import { Link, useLocation } from 'react-router-dom'
import routes from '../client-routes'

type RouteItem = {
  path?: string
  name?: string
}

type Crumb = {
  pathname: string
  name: string
  active: boolean
}

const AppBreadcrumb: React.FC = () => {
  const currentLocation = useLocation().pathname

  const getRouteName = (pathname: string, rs: RouteItem[]) => {
    const currentRoute = rs.find((r) => r.path === pathname)
    return currentRoute?.name || ''
  }

  const getBreadcrumbs = (location: string) => {
    const breadcrumbs: Crumb[] = []
    location
      .split('/')
      .filter(Boolean)
      .reduce((prev, curr, index, array) => {
        const currentPathname = `${prev}/${curr}`.replace(/\/+/g, '/')
        const routeName = getRouteName(currentPathname, routes as RouteItem[])
        if (routeName) {
          breadcrumbs.push({
            pathname: currentPathname,
            name: routeName,
            active: index + 1 === array.length,
          })
        }
        return currentPathname
      }, '')
    return breadcrumbs
  }

  const breadcrumbs = getBreadcrumbs(currentLocation)

  if (!breadcrumbs.length) return null

  return (
    <nav
      aria-label="breadcrumb"
      style={{
        whiteSpace: 'nowrap',
        overflowX: 'auto',
      }}
    >
      <ol
        className="breadcrumb my-0 d-flex flex-wrap align-items-center"
        style={{ gap: '0.5rem', marginBottom: 0 }}
      >
        {breadcrumbs.map((breadcrumb, idx) => (
          <li
            key={`${breadcrumb.pathname}-${idx}`}
            className={`breadcrumb-item ${breadcrumb.active ? 'active' : ''}`}
            aria-current={breadcrumb.active ? 'page' : undefined}
            style={{ fontSize: '14px' }}
          >
            {breadcrumb.active ? (
              <span style={{ color: '#6c757d' }}>{breadcrumb.name}</span>
            ) : (
              <Link
                to={breadcrumb.pathname}
                style={{
                  color: '#0d6efd',
                  textDecoration: 'none',
                }}
              >
                {breadcrumb.name}
              </Link>
            )}
          </li>
        ))}
      </ol>
    </nav>
  )
}

export default React.memo(AppBreadcrumb)












ERROR in src/Client/layout/AppContent.tsx:17:28
TS2786: 'CSpinner' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    15 |   return (
    16 |     <CContainer className="px-4" lg style={{ marginTop: '35px' }}>
  > 17 |       <Suspense fallback={<CSpinner color="primary" />}>
       |                            ^^^^^^^^
    18 |         <Routes>
    19 |           {(routes as RouteItem[]).map((route, idx) => {
    20 |             return (

ERROR in src/Client/layout/AppHeader.tsx:95:12
TS2786: 'CHeaderNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    93 |           </CHeaderToggler>
    94 |
  > 95 |           <CHeaderNav className="d-none d-md-flex ms-3">
       |            ^^^^^^^^^^
    96 |             <CNavItem>
    97 |               <div className="fiservLogo">
    98 |                 <img src={fiservLogo} alt="Fiserv Logo" />

ERROR in src/Client/layout/AppHeader.tsx:96:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    94 |
    95 |           <CHeaderNav className="d-none d-md-flex ms-3">
  > 96 |             <CNavItem>
       |              ^^^^^^^^
    97 |               <div className="fiservLogo">
    98 |                 <img src={fiservLogo} alt="Fiserv Logo" />
    99 |               </div>

ERROR in src/Client/layout/AppHeader.tsx:103:12
TS2786: 'CHeaderNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    101 |           </CHeaderNav>
    102 |
  > 103 |           <CHeaderNav className="d-none d-md-flex ms-3">
        |            ^^^^^^^^^^
    104 |             <CNavItem>
    105 |               <div className="stroke">
    106 |                 <img src={strokeImg} alt="Stroke" />

ERROR in src/Client/layout/AppHeader.tsx:104:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    102 |
    103 |           <CHeaderNav className="d-none d-md-flex ms-3">
  > 104 |             <CNavItem>
        |              ^^^^^^^^
    105 |               <div className="stroke">
    106 |                 <img src={strokeImg} alt="Stroke" />
    107 |               </div>

ERROR in src/Client/layout/AppHeader.tsx:111:12
TS2786: 'CHeaderNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    109 |           </CHeaderNav>
    110 |
  > 111 |           <CHeaderNav className="d-none d-md-flex ms-3">
        |            ^^^^^^^^^^
    112 |             <CNavItem>
    113 |               <div className="r-img">
    114 |                 <img src={rImg} alt="R" />

ERROR in src/Client/layout/AppHeader.tsx:112:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    110 |
    111 |           <CHeaderNav className="d-none d-md-flex ms-3">
  > 112 |             <CNavItem>
        |              ^^^^^^^^
    113 |               <div className="r-img">
    114 |                 <img src={rImg} alt="R" />
    115 |               </div>

ERROR in src/Client/layout/AppHeader.tsx:119:12
TS2786: 'CHeaderNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    117 |           </CHeaderNav>
    118 |
  > 119 |           <CHeaderNav className="d-none d-md-flex ms-3">
        |            ^^^^^^^^^^
    120 |             <CNavItem>
    121 |               <div className="rapid-layout">
    122 |                 <h5 className="rapid">Rapid</h5>

ERROR in src/Client/layout/AppHeader.tsx:120:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    118 |
    119 |           <CHeaderNav className="d-none d-md-flex ms-3">
  > 120 |             <CNavItem>
        |              ^^^^^^^^
    121 |               <div className="rapid-layout">
    122 |                 <h5 className="rapid">Rapid</h5>
    123 |               </div>

ERROR in src/Client/layout/AppHeader.tsx:125:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    123 |               </div>
    124 |             </CNavItem>
  > 125 |             <CNavItem>
        |              ^^^^^^^^
    126 |               <span className="admin">Admin</span>
    127 |             </CNavItem>
    128 |           </CHeaderNav>

ERROR in src/Client/layout/AppHeader.tsx:130:12
TS2786: 'CHeaderNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    128 |           </CHeaderNav>
    129 |
  > 130 |           <CHeaderNav className="ms-auto d-flex align-items-center">
        |            ^^^^^^^^^^
    131 |             <CNavItem>
    132 |               <CNavLink href="#" className="text-white">
    133 |                 <CIcon icon={cilBell} size="lg" />

ERROR in src/Client/layout/AppHeader.tsx:131:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    129 |
    130 |           <CHeaderNav className="ms-auto d-flex align-items-center">
  > 131 |             <CNavItem>
        |              ^^^^^^^^
    132 |               <CNavLink href="#" className="text-white">
    133 |                 <CIcon icon={cilBell} size="lg" />
    134 |               </CNavLink>

ERROR in src/Client/layout/AppHeader.tsx:132:16
TS2786: 'CNavLink' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    130 |           <CHeaderNav className="ms-auto d-flex align-items-center">
    131 |             <CNavItem>
  > 132 |               <CNavLink href="#" className="text-white">
        |                ^^^^^^^^
    133 |                 <CIcon icon={cilBell} size="lg" />
    134 |               </CNavLink>
    135 |             </CNavItem>

ERROR in src/Client/layout/AppHeader.tsx:136:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    134 |               </CNavLink>
    135 |             </CNavItem>
  > 136 |             <CNavItem>
        |              ^^^^^^^^
    137 |               <CNavLink href="#" className="text-white">
    138 |                 <CIcon icon={cilList} size="lg" />
    139 |               </CNavLink>

ERROR in src/Client/layout/AppHeader.tsx:137:16
TS2786: 'CNavLink' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    135 |             </CNavItem>
    136 |             <CNavItem>
  > 137 |               <CNavLink href="#" className="text-white">
        |                ^^^^^^^^
    138 |                 <CIcon icon={cilList} size="lg" />
    139 |               </CNavLink>
    140 |             </CNavItem>

ERROR in src/Client/layout/AppHeader.tsx:141:14
TS2786: 'CNavItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    139 |               </CNavLink>
    140 |             </CNavItem>
  > 141 |             <CNavItem>
        |              ^^^^^^^^
    142 |               <CNavLink
    143 |                 href="http://localhost:3001"
    144 |                 target="_blank"

ERROR in src/Client/layout/AppHeader.tsx:142:16
TS2786: 'CNavLink' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    140 |             </CNavItem>
    141 |             <CNavItem>
  > 142 |               <CNavLink
        |                ^^^^^^^^
    143 |                 href="http://localhost:3001"
    144 |                 target="_blank"
    145 |                 rel="noreferrer"

ERROR in src/Client/layout/AppHeader.tsx:156:14
TS2786: 'CDropdown' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    154 |             </li>
    155 |
  > 156 |             <CDropdown variant="nav-item" placement="bottom-end">
        |              ^^^^^^^^^
    157 |               <CDropdownToggle caret={false} className="text-white">
    158 |                 {colorMode === 'dark' ? (
    159 |                   <CIcon icon={cilMoon} size="lg" />

ERROR in src/Client/layout/AppHeader.tsx:166:16
TS2786: 'CDropdownMenu' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    164 |                 )}
    165 |               </CDropdownToggle>
  > 166 |               <CDropdownMenu>
        |                ^^^^^^^^^^^^^
    167 |                 <CDropdownItem
    168 |                   active={colorMode === 'light'}
    169 |                   className="d-flex align-items-center"

ERROR in src/Client/layout/AppHeader.tsx:167:18
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    165 |               </CDropdownToggle>
    166 |               <CDropdownMenu>
  > 167 |                 <CDropdownItem
        |                  ^^^^^^^^^^^^^
    168 |                   active={colorMode === 'light'}
    169 |                   className="d-flex align-items-center"
    170 |                   as="button"

ERROR in src/Client/layout/AppHeader.tsx:176:18
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    174 |                   <CIcon className="me-2" icon={cilSun} size="lg" /> Light
    175 |                 </CDropdownItem>
  > 176 |                 <CDropdownItem
        |                  ^^^^^^^^^^^^^
    177 |                   active={colorMode === 'dark'}
    178 |                   className="d-flex align-items-center"
    179 |                   as="button"

ERROR in src/Client/layout/AppHeader.tsx:185:18
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    183 |                   <CIcon className="me-2" icon={cilMoon} size="lg" /> Dark
    184 |                 </CDropdownItem>
  > 185 |                 <CDropdownItem
        |                  ^^^^^^^^^^^^^
    186 |                   active={colorMode === 'auto'}
    187 |                   className="d-flex align-items-center"
    188 |                   as="button"

ERROR in src/Client/layout/AppHeaderDropdown.tsx:31:6
TS2786: 'CDropdown' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    Type 'undefined' is not assignable to type 'Element | null'.
    29 | const AppHeaderDropdown: React.FC = () => {
    30 |   return (
  > 31 |     <CDropdown variant="nav-item">
       |      ^^^^^^^^^
    32 |       <CDropdownToggle placement="bottom-end" className="py-0 pe-0" caret={false}>
    33 |         <CAvatar src={avatar8} size="md" />
    34 |       </CDropdownToggle>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:32:24
TS2322: Type '{ children: Element; placement: string; className: string; caret: false; }' is not assignable to type 'IntrinsicAttributes & CDropdownToggleProps'.
  Property 'placement' does not exist on type 'IntrinsicAttributes & CDropdownToggleProps'.
    30 |   return (
    31 |     <CDropdown variant="nav-item">
  > 32 |       <CDropdownToggle placement="bottom-end" className="py-0 pe-0" caret={false}>
       |                        ^^^^^^^^^
    33 |         <CAvatar src={avatar8} size="md" />
    34 |       </CDropdownToggle>
    35 |       <CDropdownMenu className="pt-0" placement="bottom-end">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:35:8
TS2786: 'CDropdownMenu' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    33 |         <CAvatar src={avatar8} size="md" />
    34 |       </CDropdownToggle>
  > 35 |       <CDropdownMenu className="pt-0" placement="bottom-end">
       |        ^^^^^^^^^^^^^
    36 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
    37 |         <CDropdownItem href="#">
    38 |           <CIcon icon={cilBell} className="me-2" />

ERROR in src/Client/layout/AppHeaderDropdown.tsx:35:39
TS2322: Type '{ children: Element[]; className: string; placement: string; }' is not assignable to type 'IntrinsicAttributes & Omit<DetailedHTMLProps<HTMLAttributes<HTMLUListElement>, HTMLUListElement>, Props<...> & CDropdownMenuProps> & Props<...> & CDropdownMenuProps & { ...; }'.
  Property 'placement' does not exist on type 'IntrinsicAttributes & Omit<DetailedHTMLProps<HTMLAttributes<HTMLUListElement>, HTMLUListElement>, Props<...> & CDropdownMenuProps> & Props<...> & CDropdownMenuProps & { ...; }'.
    33 |         <CAvatar src={avatar8} size="md" />
    34 |       </CDropdownToggle>
  > 35 |       <CDropdownMenu className="pt-0" placement="bottom-end">
       |                                       ^^^^^^^^^
    36 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
    37 |         <CDropdownItem href="#">
    38 |           <CIcon icon={cilBell} className="me-2" />

ERROR in src/Client/layout/AppHeaderDropdown.tsx:36:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    34 |       </CDropdownToggle>
    35 |       <CDropdownMenu className="pt-0" placement="bottom-end">
  > 36 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
       |          ^^^^^^^^^^^^^^^
    37 |         <CDropdownItem href="#">
    38 |           <CIcon icon={cilBell} className="me-2" />
    39 |           Updates

ERROR in src/Client/layout/AppHeaderDropdown.tsx:37:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    35 |       <CDropdownMenu className="pt-0" placement="bottom-end">
    36 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">Account</CDropdownHeader>
  > 37 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    38 |           <CIcon icon={cilBell} className="me-2" />
    39 |           Updates
    40 |           <CBadge color="info" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:40:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    38 |           <CIcon icon={cilBell} className="me-2" />
    39 |           Updates
  > 40 |           <CBadge color="info" className="ms-2">
       |            ^^^^^^
    41 |             42
    42 |           </CBadge>
    43 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:44:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    42 |           </CBadge>
    43 |         </CDropdownItem>
  > 44 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    45 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    46 |           Messages
    47 |           <CBadge color="success" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:47:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    45 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    46 |           Messages
  > 47 |           <CBadge color="success" className="ms-2">
       |            ^^^^^^
    48 |             42
    49 |           </CBadge>
    50 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:51:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    49 |           </CBadge>
    50 |         </CDropdownItem>
  > 51 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    52 |           <CIcon icon={cilTask} className="me-2" />
    53 |           Tasks
    54 |           <CBadge color="danger" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:54:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    52 |           <CIcon icon={cilTask} className="me-2" />
    53 |           Tasks
  > 54 |           <CBadge color="danger" className="ms-2">
       |            ^^^^^^
    55 |             42
    56 |           </CBadge>
    57 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:58:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    56 |           </CBadge>
    57 |         </CDropdownItem>
  > 58 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    59 |           <CIcon icon={cilCommentSquare} className="me-2" />
    60 |           Comments
    61 |           <CBadge color="warning" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:61:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    59 |           <CIcon icon={cilCommentSquare} className="me-2" />
    60 |           Comments
  > 61 |           <CBadge color="warning" className="ms-2">
       |            ^^^^^^
    62 |             42
    63 |           </CBadge>
    64 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:65:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    63 |           </CBadge>
    64 |         </CDropdownItem>
  > 65 |         <CDropdownHeader className="bg-body-secondary fw-semibold my-2">Settings</CDropdownHeader>
       |          ^^^^^^^^^^^^^^^
    66 |         <CDropdownItem href="#">
    67 |           <CIcon icon={cilUser} className="me-2" />
    68 |           Profile

ERROR in src/Client/layout/AppHeaderDropdown.tsx:66:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    64 |         </CDropdownItem>
    65 |         <CDropdownHeader className="bg-body-secondary fw-semibold my-2">Settings</CDropdownHeader>
  > 66 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    67 |           <CIcon icon={cilUser} className="me-2" />
    68 |           Profile
    69 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:70:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    68 |           Profile
    69 |         </CDropdownItem>
  > 70 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    71 |           <CIcon icon={cilSettings} className="me-2" />
    72 |           Settings
    73 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:74:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    72 |           Settings
    73 |         </CDropdownItem>
  > 74 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    75 |           <CIcon icon={cilCreditCard} className="me-2" />
    76 |           Payments
    77 |           <CBadge color="secondary" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:77:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    75 |           <CIcon icon={cilCreditCard} className="me-2" />
    76 |           Payments
  > 77 |           <CBadge color="secondary" className="ms-2">
       |            ^^^^^^
    78 |             42
    79 |           </CBadge>
    80 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:81:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    79 |           </CBadge>
    80 |         </CDropdownItem>
  > 81 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    82 |           <CIcon icon={cilFile} className="me-2" />
    83 |           Projects
    84 |           <CBadge color="primary" className="ms-2">

ERROR in src/Client/layout/AppHeaderDropdown.tsx:84:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    82 |           <CIcon icon={cilFile} className="me-2" />
    83 |           Projects
  > 84 |           <CBadge color="primary" className="ms-2">
       |            ^^^^^^
    85 |             42
    86 |           </CBadge>
    87 |         </CDropdownItem>

ERROR in src/Client/layout/AppHeaderDropdown.tsx:89:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    87 |         </CDropdownItem>
    88 |         <CDropdownDivider />
  > 89 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    90 |           <CIcon icon={cilLockLocked} className="me-2" />
    91 |           Lock Account
    92 |         </CDropdownItem>

ERROR in src/Client/layout/AppSidebar.tsx:23:6
TS2786: 'CSidebar' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    21 |
    22 |   return (
  > 23 |     <CSidebar
       |      ^^^^^^^^
    24 |       colorScheme="light"
    25 |       position="fixed"
    26 |       unfoldable={unfoldable}

ERROR in src/Client/layout/AppSidebar.tsx:40:8
TS2786: 'CSidebarNav' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    38 |       </CSidebarHeader>
    39 |
  > 40 |       <CSidebarNav>
       |        ^^^^^^^^^^^
    41 |         <AppSidebarNav items={defaultNav} />
    42 |       </CSidebarNav>
    43 |

ERROR in src/Client/layout/AppSidebar.tsx:41:24
TS2322: Type '({ component: () => Element; name?: undefined; to?: undefined; icon?: undefined; badge?: undefined; } | { component: PolymorphicRefForwardingComponent<"li", CNavItemProps>; name: string; to: string; icon: Element; badge: { ...; }; } | { ...; })[]' is not assignable to type 'NavItem[]'.
  Type '{ component: () => Element; name?: undefined; to?: undefined; icon?: undefined; badge?: undefined; } | { component: PolymorphicRefForwardingComponent<"li", CNavItemProps>; name: string; to: string; icon: Element; badge: { ...; }; } | { ...; }' is not assignable to type 'NavItem'.
    Type '{ component: PolymorphicRefForwardingComponent<"li", CNavItemProps>; name: string; to: string; icon: JSX.Element; badge: { ...; }; }' is not assignable to type 'NavItem'.
      Types of property 'component' are incompatible.
        Type 'PolymorphicRefForwardingComponent<"li", CNavItemProps>' is not assignable to type 'ElementType<any, keyof IntrinsicElements>'.
          Type 'PolymorphicRefForwardingComponent<"li", CNavItemProps>' is not assignable to type 'FunctionComponent<any>'.
            Type 'ReactNode' is not assignable to type 'ReactElement<any, any> | null'.
              Type 'undefined' is not assignable to type 'ReactElement<any, any> | null'.
    39 |
    40 |       <CSidebarNav>
  > 41 |         <AppSidebarNav items={defaultNav} />
       |                        ^^^^^
    42 |       </CSidebarNav>
    43 |
    44 |       <CSidebarFooter className="border-top d-none d-lg-flex">

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
