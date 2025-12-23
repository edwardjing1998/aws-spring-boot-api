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

// ✅ IMPORTANT: correct relative path for:
// src/Client/layout/header/AppHeaderDropdown.tsx  ->  src/Client/assets/images/Avatar.png
import avatar8 from '../../assets/images/Avatar.png'

const AppHeaderDropdown: React.FC = () => {
  const [open, setOpen] = useState(false)
  const rootRef = useRef<HTMLLIElement>(null)

  useEffect(() => {
    const onDocClick = (e: MouseEvent) => {
      const target = e.target as Node
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
        className="btn btn-link nav-link py-0 pe-0"
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
      <ul className={`dropdown-menu dropdown-menu-end pt-0 ${open ? 'show' : ''}`}>
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
