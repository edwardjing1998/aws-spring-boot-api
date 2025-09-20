import React, { useEffect, useRef, Fragment } from 'react'
import { NavLink } from 'react-router-dom'
import { useSelector, useDispatch } from 'react-redux'
import '../scss/header.scss'
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

import {
  CRow,
  CCol,
} from '@coreui/react';

const AppHeader = () => {
  const headerRef = useRef()
  const { colorMode, setColorMode } = useColorModes('coreui-free-react-admin-template-theme')
  const dispatch = useDispatch()
  const sidebarShow = useSelector((state) => state.sidebarShow)

  // total height = top bar (55) + breadcrumb bar (36) = 91
  const HEADER_HEIGHT = 55
  const BREADCRUMB_HEIGHT = 36
  const TOTAL_HEADER_HEIGHT = HEADER_HEIGHT + BREADCRUMB_HEIGHT

  useEffect(() => {
    document.addEventListener('scroll', () => {
      headerRef.current &&
        headerRef.current.classList.toggle('shadow-sm', document.documentElement.scrollTop > 0)
    })
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
                <img src="src/assets/images/FiservLogo.png" alt="Fiserv Logo" />
              </div>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="stroke">
                <img src="src/assets/images/Stroke.png" alt="Stroke" />
              </div>
            </CNavItem>
          </CHeaderNav>

          <CHeaderNav className="d-none d-md-flex ms-3">
            <CNavItem>
              <div className="r-img">
                <img src="src/assets/images/R.png" alt="R" />
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
              <CNavLink href="#" className="text-white">
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
