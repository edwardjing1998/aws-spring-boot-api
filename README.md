import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import {
  CCloseButton,
  CSidebar,
  CSidebarFooter,
  CSidebarHeader,
  CSidebarToggler,
  CSidebarNav,
} from '@coreui/react'

import { AppSidebarNav } from './AppSidebarNav'

// sidebar nav config
import defaultNav from '../_nav'

const AppSidebar: React.FC = () => {
  const dispatch = useDispatch()
  const unfoldable = useSelector((state: any) => state.sidebarUnfoldable)
  const sidebarShow = useSelector((state: any) => state.sidebarShow)

  return (
    <CSidebar
      colorScheme="light"
      position="fixed"
      unfoldable={unfoldable}
      visible={sidebarShow}
      onVisibleChange={(visible) => {
        dispatch({ type: 'set', sidebarShow: visible })
      }}
    >
      <CSidebarHeader>
        <CCloseButton
          className="d-lg-none"
          dark
          onClick={() => dispatch({ type: 'set', sidebarShow: false })}
        />
      </CSidebarHeader>

      <CSidebarNav>
        <AppSidebarNav items={defaultNav} />
      </CSidebarNav>

      <CSidebarFooter className="border-top d-none d-lg-flex">
        <CSidebarToggler
          onClick={() =>
            dispatch({ type: 'set', sidebarUnfoldable: !unfoldable })
          }
        />
      </CSidebarFooter>
    </CSidebar>
  )
}

export default React.memo(AppSidebar)






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
