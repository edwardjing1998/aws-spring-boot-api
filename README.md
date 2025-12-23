import React, { useEffect, useRef, useState } from 'react'
import CIcon from '@coreui/icons-react'
import {
  cilBell,
  cilCreditCard,
  cilCommentSquare,
  cilEnvelopeOpen,
  cilFile,
  cilLockLocked,
  cilSettings,
  cilTask,
  cilUser,
} from '@coreui/icons'

// IMPORTANT: match the real filename case on disk
// If your file is Avatar.png, import Avatar.png (case sensitive on some systems)
import avatar8 from '../assets/images/Avatar.png'

const AppHeaderDropdown: React.FC = () => {
  const [open, setOpen] = useState(false)
  const rootRef = useRef<HTMLLIElement>(null)

  useEffect(() => {
    const onDocClick = (e: MouseEvent) => {
      const target = e.target as HTMLElement
      if (!rootRef.current) return
      if (!rootRef.current.contains(target)) setOpen(false)
    }
    const onKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Escape') setOpen(false)
    }

    document.addEventListener('click', onDocClick)
    document.addEventListener('keydown', onKeyDown)
    return () => {
      document.removeEventListener('click', onDocClick)
      document.removeEventListener('keydown', onKeyDown)
    }
  }, [])

  return (
    <li ref={rootRef} className="nav-item dropdown">
      {/* Toggle */}
      <button
        type="button"
        className="btn btn-link nav-link p-0 py-0 pe-0 text-white"
        aria-haspopup="menu"
        aria-expanded={open}
        onClick={() => setOpen((v) => !v)}
        style={{ textDecoration: 'none' }}
      >
        <img
          src={avatar8}
          alt="User avatar"
          width={32}
          height={32}
          style={{
            borderRadius: '50%',
            objectFit: 'cover',
            display: 'block',
          }}
        />
      </button>

      {/* Menu */}
      <ul
        className={`dropdown-menu dropdown-menu-end ${open ? 'show' : ''}`}
        style={{
          marginTop: '0.5rem',
        }}
      >
        <li>
          <h6 className="dropdown-header bg-body-secondary fw-semibold mb-2">Account</h6>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilBell} className="me-2" />
            <span className="me-auto">Updates</span>
            <span className="badge bg-info ms-2">42</span>
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilEnvelopeOpen} className="me-2" />
            <span className="me-auto">Messages</span>
            <span className="badge bg-success ms-2">42</span>
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilTask} className="me-2" />
            <span className="me-auto">Tasks</span>
            <span className="badge bg-danger ms-2">42</span>
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilCommentSquare} className="me-2" />
            <span className="me-auto">Comments</span>
            <span className="badge bg-warning text-dark ms-2">42</span>
          </a>
        </li>

        <li>
          <h6 className="dropdown-header bg-body-secondary fw-semibold my-2">Settings</h6>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilUser} className="me-2" />
            Profile
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilSettings} className="me-2" />
            Settings
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilCreditCard} className="me-2" />
            <span className="me-auto">Payments</span>
            <span className="badge bg-secondary ms-2">42</span>
          </a>
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilFile} className="me-2" />
            <span className="me-auto">Projects</span>
            <span className="badge bg-primary ms-2">42</span>
          </a>
        </li>

        <li>
          <hr className="dropdown-divider" />
        </li>

        <li>
          <a className="dropdown-item d-flex align-items-center" href="#">
            <CIcon icon={cilLockLocked} className="me-2" />
            Lock Account
          </a>
        </li>
      </ul>
    </li>
  )
}

export default AppHeaderDropdown




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
