ERROR in ./src/Client/layout/AppHeaderDropdown.tsx 10:0-53
Module not found: Error: Can't resolve '../../assets/images/Avatar.png' in 'C:\Users\F2LIPBX\react\fiserv-github\release-merged\trace-ui-reactapp\trace\src\Client\layout'

webpack compiled with 1 error and 1 warning
ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:30:6
TS2786: 'CDropdown' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    Type 'undefined' is not assignable to type 'Element | null'.
    28 | const AppHeaderDropdown: React.FC = () => {
    29 |   return (
  > 30 |     <CDropdown variant="nav-item" placement="bottom-end">
       |      ^^^^^^^^^
    31 |       {/* ✅ placement REMOVED from CDropdownToggle */}
    32 |       <CDropdownToggle className="py-0 pe-0" caret={false}>
    33 |         <CAvatar src={avatar8} size="md" />

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:37:8
TS2786: 'CDropdownMenu' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    35 |
    36 |       {/* ✅ no placement on CDropdownMenu */}
  > 37 |       <CDropdownMenu className="pt-0">
       |        ^^^^^^^^^^^^^
    38 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">
    39 |           Account
    40 |         </CDropdownHeader>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:38:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    36 |       {/* ✅ no placement on CDropdownMenu */}
    37 |       <CDropdownMenu className="pt-0">
  > 38 |         <CDropdownHeader className="bg-body-secondary fw-semibold mb-2">
       |          ^^^^^^^^^^^^^^^
    39 |           Account
    40 |         </CDropdownHeader>
    41 |

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:42:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    40 |         </CDropdownHeader>
    41 |
  > 42 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    43 |           <CIcon icon={cilBell} className="me-2" />
    44 |           Updates
    45 |           <CBadge color="info" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:45:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    43 |           <CIcon icon={cilBell} className="me-2" />
    44 |           Updates
  > 45 |           <CBadge color="info" className="ms-2">
       |            ^^^^^^
    46 |             42
    47 |           </CBadge>
    48 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:50:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    48 |         </CDropdownItem>
    49 |
  > 50 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    51 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    52 |           Messages
    53 |           <CBadge color="success" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:53:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    51 |           <CIcon icon={cilEnvelopeOpen} className="me-2" />
    52 |           Messages
  > 53 |           <CBadge color="success" className="ms-2">
       |            ^^^^^^
    54 |             42
    55 |           </CBadge>
    56 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:58:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    56 |         </CDropdownItem>
    57 |
  > 58 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    59 |           <CIcon icon={cilTask} className="me-2" />
    60 |           Tasks
    61 |           <CBadge color="danger" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:61:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    59 |           <CIcon icon={cilTask} className="me-2" />
    60 |           Tasks
  > 61 |           <CBadge color="danger" className="ms-2">
       |            ^^^^^^
    62 |             42
    63 |           </CBadge>
    64 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:66:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    64 |         </CDropdownItem>
    65 |
  > 66 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    67 |           <CIcon icon={cilCommentSquare} className="me-2" />
    68 |           Comments
    69 |           <CBadge color="warning" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:69:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    67 |           <CIcon icon={cilCommentSquare} className="me-2" />
    68 |           Comments
  > 69 |           <CBadge color="warning" className="ms-2">
       |            ^^^^^^
    70 |             42
    71 |           </CBadge>
    72 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:74:10
TS2786: 'CDropdownHeader' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    72 |         </CDropdownItem>
    73 |
  > 74 |         <CDropdownHeader className="bg-body-secondary fw-semibold my-2">
       |          ^^^^^^^^^^^^^^^
    75 |           Settings
    76 |         </CDropdownHeader>
    77 |

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:78:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    76 |         </CDropdownHeader>
    77 |
  > 78 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    79 |           <CIcon icon={cilUser} className="me-2" />
    80 |           Profile
    81 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:83:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    81 |         </CDropdownItem>
    82 |
  > 83 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    84 |           <CIcon icon={cilSettings} className="me-2" />
    85 |           Settings
    86 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:88:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    86 |         </CDropdownItem>
    87 |
  > 88 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    89 |           <CIcon icon={cilCreditCard} className="me-2" />
    90 |           Payments
    91 |           <CBadge color="secondary" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:91:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    89 |           <CIcon icon={cilCreditCard} className="me-2" />
    90 |           Payments
  > 91 |           <CBadge color="secondary" className="ms-2">
       |            ^^^^^^
    92 |             42
    93 |           </CBadge>
    94 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:96:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    94 |         </CDropdownItem>
    95 |
  > 96 |         <CDropdownItem href="#">
       |          ^^^^^^^^^^^^^
    97 |           <CIcon icon={cilFile} className="me-2" />
    98 |           Projects
    99 |           <CBadge color="primary" className="ms-2">

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:99:12
TS2786: 'CBadge' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
     97 |           <CIcon icon={cilFile} className="me-2" />
     98 |           Projects
  >  99 |           <CBadge color="primary" className="ms-2">
        |            ^^^^^^
    100 |             42
    101 |           </CBadge>
    102 |         </CDropdownItem>

ERROR in src/Client/layout/header/AppHeaderDropdown.tsx:106:10
TS2786: 'CDropdownItem' cannot be used as a JSX component.
  Its return type 'ReactNode' is not a valid JSX element.
    104 |         <CDropdownDivider />
    105 |
  > 106 |         <CDropdownItem href="#">
        |          ^^^^^^^^^^^^^
    107 |           <CIcon icon={cilLockLocked} className="me-2" />
    108 |           Lock Account
    109 |         </CDropdownItem>
