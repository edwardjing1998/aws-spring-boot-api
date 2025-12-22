import React from 'react'
import { useLocation } from 'react-router-dom'
import routes from '../client-routes'
import { CBreadcrumb, CBreadcrumbItem } from '@coreui/react'

const PATH_MAPPERS = [
  { test: (path) => path.toLowerCase().includes('/edit/'), label: 'Edit' },
  { test: (path) => path.toLowerCase().includes('/maintenance/'), label: 'Maintenance' },
  { test: (path) => path.toLowerCase().includes('/report/'), label: 'Report' },
]

//client-report-mapping  

const NAME_MAPPERS = [{ match: 'DailyMessage', label: 'Daily Message' },
                      { match: 'ClientReportMapping', label: 'Client Report Mapping' } ,
                      { match: 'WebClientDirectory', label: 'Web Client Directory' },  
                      { match: 'EmailSetup', label: 'Email Setup' },
                      { match: 'EmailEventId', label: 'Email Event Id' },   
                      { match: 'ZipCodeConfig', label: 'Zip Code Config' }, 
                      { match: 'MailType', label: 'Mail Type' },        
                      ]

                                            
const mapPathToLabel = (pathname) => {
  for (const m of PATH_MAPPERS) if (m.test(pathname)) return m.label
  const lastSeg = pathname.split('/').filter(Boolean).pop() || '/'
  return lastSeg.replace(/-/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase())
}
const mapNameToLabel = (name) => {
  const hit = NAME_MAPPERS.find((m) => m.match === name)
  return hit ? hit.label : name
}

const AppBreadcrumb = () => {
  const currentLocation = useLocation().pathname

  const getRouteName = (pathname, routes) => {
    const currentRoute = routes.find((route) => route.path === pathname)
    return currentRoute ? currentRoute.name : false
  }

  const getBreadcrumbs = (location) => {
    const breadcrumbs = []
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
        const leftLabel = mapPathToLabel(breadcrumb.pathname)
        const rightLabel = mapNameToLabel(breadcrumb.name)

        const content = (
          <span
            style={{
              display: 'inline-flex',
              alignItems: 'center',
              position: 'relative',
              top: '2px',
              fontSize: '14px',          // ↓ smaller font
              color: '#0d6efd',           // ↓ blue text
            }}
          >
            <span style={{ fontFamily: 'monospace' }}>{leftLabel}</span>
            <span style={{ margin: '0 6px' }}>»</span>
            <span>{rightLabel}</span>
          </span>
        )

        return (
          <CBreadcrumbItem
            key={idx}
            {...(breadcrumb.active ? { active: true } : { href: breadcrumb.pathname })}
            style={{
              fontSize: '12px',          // ensure link wrappers also shrink
              color: '#0d6efd',          // ensure blue even when it renders an <a>
            }}
          >
            {content}
          </CBreadcrumbItem>
        )
      })}
    </CBreadcrumb>
  )
}

export default React.memo(AppBreadcrumb)
