import React, { useEffect, useRef, Fragment } from 'react'
import { NavLink } from 'react-router-dom'
import { useSelector, useDispatch } from 'react-redux'
import {
  CContainer,
  CDropdown,
  CDropdownItem,
  CDropdownMenu,
  CDropdownToggle,
  CHeader,
  CHeaderNav,
  CHeaderToggler,
  CNavLink,
  CNavItem,
  useColorModes,
  CRow,
  CCol,
} from '@coreui/react'
import CIcon from '@coreui/icons-react'
import {
  cilBell,
  cilContrast,
  cilEnvelopeOpen,
  cilList,
  cilMenu,
  cilMoon,
  cilSun,
} from '@coreui/icons'

import { AppBreadcrumb } from './index'
import { AppHeaderDropdown } from './header/index'

// Import assets (ensure these paths are correct in your project structure)
// You might need a declaration file (e.g., assets.d.ts) for image imports in TS
import fiservLogo from '../assets/images/FiservLogo.png'
import strokeImg from '../assets/images/Stroke.png'
import rImg from '../assets/images/R.png'

// Styles
import '../scss/header.scss'

const AppHeader: React.FC = () => {
  const headerRef = useRef<HTMLDivElement>(null)
  const { colorMode, setColorMode } = useColorModes('coreui-free-react-admin-template-theme')
  const dispatch = useDispatch()
  const sidebarShow = useSelector((state: any) => state.sidebarShow)

  // total height = top bar (55) + breadcrumb bar (36) = 91
  const HEADER_HEIGHT = 55
  const BREADCRUMB_HEIGHT = 36
  const TOTAL_HEADER_HEIGHT = HEADER_HEIGHT + BREADCRUMB_HEIGHT

  useEffect(() => {
    const handleScroll = () => {
      headerRef.current &&
        headerRef.current.classList.toggle('shadow-sm', document.documentElement.scrollTop > 0)
    }
    document.addEventListener('scroll', handleScroll)
    return () => {
      document.removeEventListener('scroll', handleScroll)
    }
  }, [])

  return (
    <Fragment>
      <CHeader
        ref={headerRef}
        className="p-0"
        style={{
          backgroundColor: '#096cd4',
          position: 'fixed',
          top: 0,
          left: 0,
          width: '100%',
          zIndex: 1100
        }}
      >
        <CContainer
          className="border-bottom px-4 text-white"
          fluid
          style={{
            backgroundColor: '#096cd4',
            height: `${HEADER_HEIGHT}px`,
            minHeight: `${HEADER_HEIGHT}px`,
            maxHeight: `${HEADER_HEIGHT}px`,
          }}
        >
          <CHeaderToggler
            onClick={() => dispatch({ type: 'set', sidebarShow: !sidebarShow })}
            style={{ marginInlineStart: '-14px' }}
          >
            <CIcon icon={cilMenu} size="lg" style={{ color: 'white' }} />
          </CHeaderToggler>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="fiservLogo">
                <img src={fiservLogo} alt="Fiserv Logo" />
              </div>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="stroke">
                <img src={strokeImg} alt="Stroke" />
              </div>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="r-img">
                <img src={rImg} alt="R" />
              </div>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="rapid-layout">
                <h5 className="rapid">Rapid</h5>
              </div>
            </CNavItem>
            <CNavItem>
              <span className="admin">Admin</span>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="ms-auto d-flex align-items-center">
            <CNavItem>
              <CNavLink href="#" className="text-white">
                <CIcon icon={cilBell} size="lg" />
              </CNavLink>
            </CNavItem>
            <CNavItem>
              <CNavLink href="#" className="text-white">
                <CIcon icon={cilList} size="lg" />
              </CNavLink>
            </CNavItem>
            <CNavItem>
              <CNavLink
                href="http://localhost:3001"
                target="_blank"
                rel="noreferrer"
                className="text-white"
              >
                <CIcon icon={cilEnvelopeOpen} size="lg" />
              </CNavLink>
            </CNavItem>

            <li className="nav-item py-1">
              <div className="vr h-100 mx-2 text-white text-opacity-75"></div>
            </li>

            <CDropdown variant="nav-item" placement="bottom-end">
              <CDropdownToggle caret={false} className="text-white">
                {colorMode === 'dark' ? (
                  <CIcon icon={cilMoon} size="lg" />
                ) : colorMode === 'auto' ? (
                  <CIcon icon={cilContrast} size="lg" />
                ) : (
                  <CIcon icon={cilSun} size="lg" />
                )}
              </CDropdownToggle>
              <CDropdownMenu>
                <CDropdownItem
                  active={colorMode === 'light'}
                  className="d-flex align-items-center"
                  as="button"
                  type="button"
                  onClick={() => setColorMode('light')}
                >
                  <CIcon className="me-2" icon={cilSun} size="lg" /> Light
                </CDropdownItem>
                <CDropdownItem
                  active={colorMode === 'dark'}
                  className="d-flex align-items-center"
                  as="button"
                  type="button"
                  onClick={() => setColorMode('dark')}
                >
                  <CIcon className="me-2" icon={cilMoon} size="lg" /> Dark
                </CDropdownItem>
                <CDropdownItem
                  active={colorMode === 'auto'}
                  className="d-flex align-items-center"
                  as="button"
                  type="button"
                  onClick={() => setColorMode('auto')}
                >
                  <CIcon className="me-2" icon={cilContrast} size="lg" /> Auto
                </CDropdownItem>
              </CDropdownMenu>
            </CDropdown>

            <li className="nav-item py-1">
              <div className="vr h-100 mx-2 text-white text-opacity-75"></div>
            </li>

            <AppHeaderDropdown />
          </CHeaderNav>
        </CContainer>

        {/* breadcrumb */}
        <CContainer fluid className="px-0" style={{ backgroundColor: 'white', width: '100%' }}>
          <CRow className="w-100 m-0" style={{ height: `${BREADCRUMB_HEIGHT}px` }}>
            <CCol style={{ flex: '0 0 20%', maxWidth: '20%' }}></CCol>
            <CCol style={{ flex: '0 0 80%,', maxWidth: '80%' }}>
              <AppBreadcrumb />
            </CCol>
          </CRow>
        </CContainer>
      </CHeader>

      {/* ---- spacer so content can scroll under the fixed header ---- */}
      <div className="header-offset" style={{ height: `${TOTAL_HEADER_HEIGHT}px` }} />
    </Fragment>
  )
}

export default AppHeader











import React, { useEffect, useRef, Fragment, useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { useColorModes } from '@coreui/react'
import CIcon from '@coreui/icons-react'
import { cilBell, cilContrast, cilEnvelopeOpen, cilList, cilMenu, cilMoon, cilSun } from '@coreui/icons'

import { AppBreadcrumb } from './index'
import { AppHeaderDropdown } from './header/index'

import fiservLogo from '../assets/images/FiservLogo.png'
import strokeImg from '../assets/images/Stroke.png'
import rImg from '../assets/images/R.png'

import '../scss/header.scss'

const AppHeader: React.FC = () => {
  const headerRef = useRef<HTMLElement>(null)
  const { colorMode, setColorMode } = useColorModes('coreui-free-react-admin-template-theme')
  const dispatch = useDispatch()
  const sidebarShow = useSelector((state: any) => state.sidebarShow)

  // total height = top bar (55) + breadcrumb bar (36) = 91
  const HEADER_HEIGHT = 55
  const BREADCRUMB_HEIGHT = 36
  const TOTAL_HEADER_HEIGHT = HEADER_HEIGHT + BREADCRUMB_HEIGHT

  // dropdown open state (replaces CDropdown)
  const [themeOpen, setThemeOpen] = useState(false)

  useEffect(() => {
    const handleScroll = () => {
      if (!headerRef.current) return
      headerRef.current.classList.toggle('shadow-sm', document.documentElement.scrollTop > 0)
    }
    document.addEventListener('scroll', handleScroll)

    // close dropdown on outside click / esc
    const onDocClick = (e: MouseEvent) => {
      const target = e.target as HTMLElement
      if (!target.closest?.('#theme-dropdown')) setThemeOpen(false)
    }
    const onKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Escape') setThemeOpen(false)
    }

    document.addEventListener('click', onDocClick)
    document.addEventListener('keydown', onKeyDown)

    return () => {
      document.removeEventListener('scroll', handleScroll)
      document.removeEventListener('click', onDocClick)
      document.removeEventListener('keydown', onKeyDown)
    }
  }, [])

  const ThemeIcon = () => {
    if (colorMode === 'dark') return <CIcon icon={cilMoon} size="lg" />
    if (colorMode === 'auto') return <CIcon icon={cilContrast} size="lg" />
    return <CIcon icon={cilSun} size="lg" />
  }

  return (
    <Fragment>
      <header
        ref={headerRef}
        className="p-0"
        style={{
          backgroundColor: '#096cd4',
          position: 'fixed',
          top: 0,
          left: 0,
          width: '100%',
          zIndex: 1100,
        }}
      >
        {/* Top bar */}
        <div
          className="border-bottom px-4 text-white container-fluid d-flex align-items-center"
          style={{
            backgroundColor: '#096cd4',
            height: `${HEADER_HEIGHT}px`,
            minHeight: `${HEADER_HEIGHT}px`,
            maxHeight: `${HEADER_HEIGHT}px`,
          }}
        >
          {/* Toggler */}
          <button
            type="button"
            className="btn btn-link p-0"
            onClick={() => dispatch({ type: 'set', sidebarShow: !sidebarShow })}
            style={{ marginInlineStart: '-14px', color: 'white', textDecoration: 'none' }}
            aria-label="Toggle sidebar"
          >
            <CIcon icon={cilMenu} size="lg" style={{ color: 'white' }} />
          </button>

          {/* Left brand area (match your previous layout) */}
          <nav className="d-none d-md-flex ms-3 align-items-center" aria-label="header-left">
            <div className="fiservLogo me-3">
              <img src={fiservLogo} alt="Fiserv Logo" />
            </div>

            <div className="stroke me-3">
              <img src={strokeImg} alt="Stroke" />
            </div>

            <div className="r-img me-3">
              <img src={rImg} alt="R" />
            </div>

            <div className="rapid-layout me-2">
              <h5 className="rapid mb-0">Rapid</h5>
            </div>
            <span className="admin">Admin</span>
          </nav>

          {/* Right actions */}
          <ul className="ms-auto d-flex align-items-center mb-0 list-unstyled" style={{ gap: '0.25rem' }}>
            <li className="nav-item">
              <a href="#" className="nav-link text-white">
                <CIcon icon={cilBell} size="lg" />
              </a>
            </li>

            <li className="nav-item">
              <a href="#" className="nav-link text-white">
                <CIcon icon={cilList} size="lg" />
              </a>
            </li>

            <li className="nav-item">
              <a
                href="http://localhost:3001"
                target="_blank"
                rel="noreferrer"
                className="nav-link text-white"
              >
                <CIcon icon={cilEnvelopeOpen} size="lg" />
              </a>
            </li>

            <li className="nav-item py-1">
              <div className="vr h-100 mx-2 text-white text-opacity-75"></div>
            </li>

            {/* Theme dropdown (replaces CDropdown) */}
            <li className="nav-item position-relative" id="theme-dropdown">
              <button
                type="button"
                className="btn btn-link nav-link text-white p-0"
                onClick={() => setThemeOpen((v) => !v)}
                aria-haspopup="menu"
                aria-expanded={themeOpen}
                style={{ textDecoration: 'none' }}
              >
                <ThemeIcon />
              </button>

              {themeOpen && (
                <div
                  className="dropdown-menu dropdown-menu-end show"
                  style={{ position: 'absolute', right: 0, top: '120%' }}
                >
                  <button
                    type="button"
                    className={`dropdown-item d-flex align-items-center ${colorMode === 'light' ? 'active' : ''}`}
                    onClick={() => {
                      setColorMode('light')
                      setThemeOpen(false)
                    }}
                  >
                    <CIcon className="me-2" icon={cilSun} size="lg" /> Light
                  </button>

                  <button
                    type="button"
                    className={`dropdown-item d-flex align-items-center ${colorMode === 'dark' ? 'active' : ''}`}
                    onClick={() => {
                      setColorMode('dark')
                      setThemeOpen(false)
                    }}
                  >
                    <CIcon className="me-2" icon={cilMoon} size="lg" /> Dark
                  </button>

                  <button
                    type="button"
                    className={`dropdown-item d-flex align-items-center ${colorMode === 'auto' ? 'active' : ''}`}
                    onClick={() => {
                      setColorMode('auto')
                      setThemeOpen(false)
                    }}
                  >
                    <CIcon className="me-2" icon={cilContrast} size="lg" /> Auto
                  </button>
                </div>
              )}
            </li>

            <li className="nav-item py-1">
              <div className="vr h-100 mx-2 text-white text-opacity-75"></div>
            </li>

            {/* Keep your existing dropdown component (may still be CoreUI inside).
                If it also throws TS2786, we’ll convert it too. */}
            <li className="nav-item">
              <AppHeaderDropdown />
            </li>
          </ul>
        </div>

        {/* Breadcrumb bar */}
        <div className="container-fluid px-0" style={{ backgroundColor: 'white', width: '100%' }}>
          <div className="row w-100 m-0" style={{ height: `${BREADCRUMB_HEIGHT}px` }}>
            <div className="col" style={{ flex: '0 0 20%', maxWidth: '20%' }}></div>
            <div className="col" style={{ flex: '0 0 80%', maxWidth: '80%' }}>
              <AppBreadcrumb />
            </div>
          </div>
        </div>
      </header>

      {/* spacer so content can scroll under the fixed header */}
      <div className="header-offset" style={{ height: `${TOTAL_HEADER_HEIGHT}px` }} />
    </Fragment>
  )
}

export default AppHeader







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
