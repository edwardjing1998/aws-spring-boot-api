import React from 'react'
import { useLocation } from 'react-router-dom'
import routes from '../routes' // Assuming routes is exported from a central routes file
import { CBreadcrumb, CBreadcrumbItem } from '@coreui/react'

const AppBreadcrumb = () => {
  const currentLocation = useLocation().pathname

  const getRouteName = (pathname: string, routes: any[]) => {
    const currentRoute = routes.find((route) => route.path === pathname)
    return currentRoute ? currentRoute.name : false
  }

  const getBreadcrumbs = (location: string) => {
    const breadcrumbs: any[] = []
    location.split('/').reduce((prev, curr, index, array) => {
      const currentPathname = `${prev}/${curr}`.replace(/\/+/g, '/')
      const routeName = getRouteName(currentPathname, routes)
      if (routeName) {
        breadcrumbs.push({
          pathname: currentPathname,
          name: routeName,
          active: index + 1 === array.length,
        })
      }
      return currentPathname
    })
    return breadcrumbs
  }

  const breadcrumbs = getBreadcrumbs(currentLocation)

  return (
    <CBreadcrumb
      className="my-0 d-flex flex-wrap align-items-center"
      style={{
        whiteSpace: 'nowrap',
        overflowX: 'auto',
        gap: '0.5rem',
      }}
    >
      {breadcrumbs.map((breadcrumb, idx) => {
        return (
          <CBreadcrumbItem
            key={idx}
            {...(breadcrumb.active ? { active: true } : { href: breadcrumb.pathname })}
            style={{
              fontSize: '14px',
              color: '#0d6efd',
            }}
          >
            {breadcrumb.name}
          </CBreadcrumbItem>
        )
      })}
    </CBreadcrumb>
  )
}

export default React.memo(AppBreadcrumb)
