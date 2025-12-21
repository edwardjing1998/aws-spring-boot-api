// src/App.tsx
import React, { Suspense, useEffect } from 'react'
import { Route, Routes } from 'react-router-dom'
import { useSelector } from 'react-redux'

import { CSpinner, useColorModes } from '@coreui/react'
import './scss/style.scss'
// import './scss/examples.scss'

// Containers
const DefaultLayout = React.lazy(() => import('./layout/DefaultLayout'))

// Pages
const Login = React.lazy(() => import('./views/pages/login/Login'))
const Register = React.lazy(() => import('./views/pages/register/Register'))
const Page404 = React.lazy(() => import('./views/pages/page404/Page404'))
const Page500 = React.lazy(() => import('./views/pages/page500/Page500'))

// If you already export RootState from your store, replace this with:
// import type { RootState } from './store'
type RootState = {
  theme: string
}

const App: React.FC = () => {
  const { isColorModeSet, setColorMode } = useColorModes(
    'coreui-free-react-admin-template-theme',
  )
  const storedTheme = useSelector<RootState, string>((state) => state.theme)

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.href.split('?')[1] ?? '')
    const theme = urlParams.get('theme')?.match(/^[A-Za-z0-9\s]+/i)?.[0]
    if (theme) setColorMode(theme)

    if (!isColorModeSet()) {
      setColorMode(storedTheme)
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [])

  return (
    <Suspense
      fallback={
        <div className="pt-3 text-center">
          <CSpinner color="primary" variant="grow" />
        </div>
      }
    >
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/404" element={<Page404 />} />
        <Route path="/500" element={<Page500 />} />
        <Route path="*" element={<DefaultLayout />} />
      </Routes>
    </Suspense>
  )
}

export default App





import React from 'react'
import DeleteCase from './views/rapid-admin-edit/delete-case/DeleteCase'

const Dashboard = React.lazy(() => import('./views/dashboard/Dashboard'))
const ArchiveDashboard = React.lazy(() => import('./views/dashboard/ArchiveDashboard'))

const Colors = React.lazy(() => import('./views/theme/colors/Colors'))
const Typography = React.lazy(() => import('./views/theme/typography/Typography'))

// Base
const Accordion = React.lazy(() => import('./views/base/accordion/Accordion'))
const Breadcrumbs = React.lazy(() => import('./views/base/breadcrumbs/Breadcrumbs'))
const Cards = React.lazy(() => import('./views/base/cards/Cards'))
const Carousels = React.lazy(() => import('./views/base/carousels/Carousels'))
const Collapses = React.lazy(() => import('./views/base/collapses/Collapses'))
const ListGroups = React.lazy(() => import('./views/base/list-groups/ListGroups'))
const Navs = React.lazy(() => import('./views/base/navs/Navs'))
const Paginations = React.lazy(() => import('./views/base/paginations/Paginations'))
const Placeholders = React.lazy(() => import('./views/base/placeholders/Placeholders'))
const Popovers = React.lazy(() => import('./views/base/popovers/Popovers'))
const Progress = React.lazy(() => import('./views/base/progress/Progress'))
const Spinners = React.lazy(() => import('./views/base/spinners/Spinners'))
const Tabs = React.lazy(() => import('./views/base/tabs/Tabs'))
const Tables = React.lazy(() => import('./views/base/tables/Tables'))
const Tooltips = React.lazy(() => import('./views/base/tooltips/Tooltips'))

// Buttons
const Buttons = React.lazy(() => import('./views/buttons/buttons/Buttons'))
const ButtonGroups = React.lazy(() => import('./views/buttons/button-groups/ButtonGroups'))
const Dropdowns = React.lazy(() => import('./views/buttons/dropdowns/Dropdowns'))

//Forms
const ChecksRadios = React.lazy(() => import('./views/forms/checks-radios/ChecksRadios'))
const FloatingLabels = React.lazy(() => import('./views/forms/floating-labels/FloatingLabels'))
const FormControl = React.lazy(() => import('./views/forms/form-control/FormControl'))
const InputGroup = React.lazy(() => import('./views/forms/input-group/InputGroup'))
const Layout = React.lazy(() => import('./views/forms/layout/Layout'))
const Range = React.lazy(() => import('./views/forms/range/Range'))
const Select = React.lazy(() => import('./views/forms/select/Select'))
const Validation = React.lazy(() => import('./views/forms/validation/Validation'))

const Charts = React.lazy(() => import('./views/charts/Charts'))

// Icons
const CoreUIIcons = React.lazy(() => import('./views/icons/coreui-icons/CoreUIIcons'))
const Flags = React.lazy(() => import('./views/icons/flags/Flags'))
const Brands = React.lazy(() => import('./views/icons/brands/Brands'))

// Notifications
const Alerts = React.lazy(() => import('./views/notifications/alerts/Alerts'))
const Badges = React.lazy(() => import('./views/notifications/badges/Badges'))
const Modals = React.lazy(() => import('./views/notifications/modals/Modals'))
const Toasts = React.lazy(() => import('./views/notifications/toasts/Toasts'))

const Widgets = React.lazy(() => import('./views/widgets/Widgets'))

// Rapid Admin -> Edit
const SysPrinConfig = React.lazy(() => import('./views/rapid-admin-edit/sys-pin-config/SysPrinConfig'))
const ClientInformationPanel = React.lazy(() => import('./views/rapid-admin-edit/client-information/ClientInformationPanel'))
const ClientInformationPage = React.lazy(() => import('./modules/edit/client-information/ClientInformationPage'))


const GlobalSettingForm = React.lazy(() => import('./views/rapid-admin-edit/global-setting/GlobalSettingForm'))
const DailyMessage = React.lazy(() => import('./views/rapid-admin-edit/daily-message/DailyMessage'))
const ClientAutoCompleteInput = React.lazy(() => import('./views/rapid-admin-edit/client-search-input/ClientAutoCompleteInput'))
const ReceivingFiles = React.lazy(() => import('./views/rapid-admin-edit/receiving-files/ReceivingFiles'))
const EmailSetup = React.lazy(() => import('./views/rapid-admin-edit/email-setup/EmailSetup'))

const MailType = React.lazy(() => import('./views/rapid-admin-edit/mail-type/MailType'))


const ReviewDeletedCase = React.lazy(() => import('./views/rapid-admin-edit/review-deleted-case/ReviewDeletedCase'))
const DeletedCase = React.lazy(() => import('./views/rapid-admin-edit/delete-case/DeleteCase'))

const DailyActivity = React.lazy(() => import('./views/rapid-admin-report/daily-activity/DailyActivity'))
const DailyReturnDestroy = React.lazy(() => import('./views/rapid-admin-report/daily-return-destroy/DailyReturnDestroy'))
const Inventory = React.lazy(() => import('./views/rapid-admin-report/inventory/Inventory'))
const InventoryListing = React.lazy(() => import('./views/rapid-admin-report/inventory-listing/InventoryListing'))
const InventoryReceived = React.lazy(() => import('./views/rapid-admin-report/inventory-received/InventoryReceived'))

const ProductivityReport = React.lazy(() => import('./views/rapid-admin-report/productivity/ProductivityReport'))

const SysPrinConfigs = React.lazy(() => import('./views/rapid-admin-edit/sys-pin-config/SysPrinConfigs'))
const ZipCodeConfig = React.lazy(() => import('./views/rapid-admin-edit/zip-code-config/ZipcodeConfig'))

const AddressChange = React.lazy(() => import('./views/rapid-admin-report/address-change/AddressChange'))

const ClientReportMapping = React.lazy(() => import('./views/rapid-admin-maintenance/client-report-mapping/ClientReportMapping'))
const WebClientDirectory = React.lazy(() => import('./views/rapid-admin-maintenance/web-client-directory/WebClientDirectory'))

const InputRobotTotals = React.lazy(() => import('./views/rapid-admin-report/productivity/InputRobotTotals/InputRobotTotals'))

const EmailEventId = React.lazy(() => import('./views/rapid-admin-report/EmailEventId/EmailEventId'))


const routes = [
  { path: '/', exact: true, name: 'Home' },
  { path: '/dashboard', name: 'Dashboard', element: Dashboard },
  { path: '/archive-dashboard', name: 'ArchiveDashboard', element: ArchiveDashboard },

  { path: '/theme', name: 'Theme', element: Colors, exact: true },

  { path: '/theme/colors', name: 'Colors', element: Carousels },
  { path: '/maintenance/client-report-mapping', name: 'ClientReportMapping', element: ClientReportMapping },
  { path: '/maintenance/resend-web-reports', name: 'Colors', element: Spinners },
  { path: '/maintenance/web-client-directory', name: 'WebClientDirectory', element: WebClientDirectory },

  { path: '/report/unmatch-sys-prins', name: 'Colors', element: ChecksRadios },
  { path: '/report/billing', name: 'Colors', element: Alerts },
  { path: '/report/report-queries', name: 'Colors', element: Badges },
  { path: '/report/email-event-id', name: 'EmailEventId', element: EmailEventId },
  { path: '/report/input-rebot-totals', name: 'InputRobotTotals', element: InputRobotTotals },

  { path: '/report/billing', name: 'Colors', element: Toasts },
  { path: '/report/resend-email-reports', name: 'Colors', element: Toasts },
  { path: '/report/report-queries', name: 'Colors', element: Toasts },
  { path: '/report/email-event-id', name: 'Colors', element: Toasts },

  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },

  { path: '/archive-query-maintenance/c3-file-transfer', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },
  { path: '/archive-query-maintenance/table-load', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },


  { path: '/query-maintenance/c3-file-transfer', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/data-definitions', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/table-load', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/table-load-column-mapping', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/tool-tips', name: 'Colors', element: ChecksRadios },

  { path: '/report/address-change', name: 'AddressChange', element: AddressChange },

  { path: '/report/mails-with-a-stat', name: 'Colors', element: ChecksRadios },
  { path: '/report/status', name: 'Colors', element: ChecksRadios },
  { path: '/report/pending-cis', name: 'Colors', element: ChecksRadios },
  { path: '/report/failed-non-mons', name: 'Colors', element: ChecksRadios },
  { path: '/report/robot-labels', name: 'Colors', element: ChecksRadios },


  { path: '/query-maintenance/define-query', name: 'Colors', element: ChecksRadios },
  { path: '/query-maintenance/c3-file-transfer', name: 'Colors', element: Alerts },
  { path: '/query-maintenance/data-definitions', name: 'Colors', element: Badges },
  { path: '/query-maintenance/schedule-batch-report', name: 'Colors', element: Range },
  { path: '/query-maintenance/table-load', name: 'Colors', element: Toasts },
  { path: '/query-maintenance/table-load-column-mapping', name: 'Colors', element: Range },
  { path: '/query-maintenance/tool-tips', name: 'Colors', element: Toasts },


  { path: '/archive-maintenance/client-report-mapping', name: 'Colors', element: Toasts },
  { path: '/archive-maintenance/resend-web-reports', name: 'Colors', element: Spinners },
  { path: '/archive-maintenance/web-client-directory', name: 'Colors', element: Tooltips },
  { path: '/archive-report/billing', name: 'Colors', element: Alerts },
  { path: '/archive-maintenance/input-robot-totals', name: 'Colors', element: Alerts },
  { path: '/archive-report/unmatch-sys-prins', name: 'Colors', element: Alerts },
  { path: '/archive-report/report-queries', name: 'Colors', element: Alerts },
  { path: '/archive-report/email-event-id', name: 'Colors', element: Range },
  { path: '/archive-query-maintenance/tool-tips', name: 'Colors', element: Range },
  { path: '/archive-query-maintenance/schedule-batch-report', name: 'Colors', element: Range },
  { path: '/archive-query-maintenance/data-definitions', name: 'Spinners', element: Spinners },
  { path: '/archive-query-maintenance/define-query', name: 'Spinners', element: Spinners },
  { path: 'archive-query-maintenance/table-load-column-mapping', name: 'Spinners', element: Spinners },

  { path: '/theme/typography', name: 'Typography', element: Typography },
  { path: '/base', name: 'Base', element: Cards, exact: true },
  { path: '/base/accordion', name: 'Accordion', element: Accordion },
  { path: '/base/breadcrumbs', name: 'Breadcrumbs', element: Breadcrumbs },
  { path: '/base/cards', name: 'Cards', element: Cards },
  { path: '/base/carousels', name: 'Carousel', element: Carousels },
  { path: '/base/collapses', name: 'Collapse', element: Collapses },
  { path: '/base/list-groups', name: 'List Groups', element: ListGroups },
  { path: '/base/navs', name: 'Navs', element: Navs },
  { path: '/base/paginations', name: 'Paginations', element: Paginations },
  { path: '/base/placeholders', name: 'Placeholders', element: Placeholders },
  { path: '/base/popovers', name: 'Popovers', element: Popovers },
  { path: '/base/progress', name: 'Progress', element: Progress },
  { path: '/base/spinners', name: 'Spinners', element: Spinners },
  { path: '/base/tabs', name: 'Tabs', element: Tabs },
  { path: '/base/tables', name: 'Tables', element: Tables },
  { path: '/base/tooltips', name: 'Tooltips', element: Tooltips },
  { path: '/buttons', name: 'Buttons', element: Buttons, exact: true },
  { path: '/buttons/buttons', name: 'Buttons', element: Buttons },
  { path: '/buttons/dropdowns', name: 'Dropdowns', element: Dropdowns },
  { path: '/buttons/button-groups', name: 'Button Groups', element: ButtonGroups },
  { path: '/charts', name: 'Charts', element: Charts },
  { path: '/forms', name: 'Forms', element: FormControl, exact: true },
  { path: '/forms/form-control', name: 'Form Control', element: FormControl },
  { path: '/forms/select', name: 'Select', element: Select },
  { path: '/forms/checks-radios', name: 'Checks & Radios', element: ChecksRadios },
  { path: '/forms/range', name: 'Range', element: Range },
  { path: '/forms/input-group', name: 'Input Group', element: InputGroup },
  { path: '/forms/floating-labels', name: 'Floating Labels', element: FloatingLabels },
  { path: '/forms/layout', name: 'Layout', element: Layout },
  { path: '/forms/validation', name: 'Validation', element: Validation },
  { path: '/icons', exact: true, name: 'Icons', element: CoreUIIcons },
  { path: '/icons/coreui-icons', name: 'CoreUI Icons', element: CoreUIIcons },
  { path: '/icons/flags', name: 'Flags', element: Flags },
  { path: '/icons/brands', name: 'Brands', element: Brands },
  { path: '/notifications', name: 'Notifications', element: Alerts, exact: true },
  { path: '/notifications/alerts', name: 'Alerts', element: Alerts },
  { path: '/notifications/badges', name: 'Badges', element: Badges },
  { path: '/notifications/modals', name: 'Modals', element: Modals },
  { path: '/notifications/toasts', name: 'Toasts', element: Toasts },
  { path: '/widgets', name: 'Widgets', element: Widgets },

  // Edit
  { path: '/edit/global-settings', name: 'GlobalSettingForm', element: GlobalSettingForm },
  { path: '/edit/daily-message', name: 'DailyMessage', element: DailyMessage },
  { path: '/edit/client-search-input', name: 'ClientAutoCompleteInput', element: ClientAutoCompleteInput },
  { path: '/edit/sys-prin-config', name: 'SysPrinConfig', element: SysPrinConfig },
  { path: '/edit/sys-prin-config-new', name: 'SysPrinConfigs', element: SysPrinConfigs },
  { path: '/edit/client-information', name: 'ClientInformationPanel', element: ClientInformationPanel },
  { path: '/edit/client-information-new', name: 'Edit / Client Information', element: ClientInformationPage },
  { path: '/eidt/receive-files', name: 'ReceivingFiles', element: ReceivingFiles },
  { path: '/edit/email-setup', name: 'EmailSetup', element: EmailSetup },
  { path: '/edit/message-table', name: 'SysPrinConfig', element: SysPrinConfig },
  { path: '/edit/zip-code-config', name: 'ZipCodeConfig', element: ZipCodeConfig },
  { path: '/edit/mail-type', name: 'MailType', element: MailType },
  { path: '/edit/delete-case', name: 'DeleteCase', element: DeleteCase },
  { path: '/edit/review-deleted-case', name: 'ReviewDeletedCase', element: ReviewDeletedCase },
  { path: '/eidt/account-number', name: 'SysPrinConfig', element: SysPrinConfig },

  { path: '/report/daily-return-destroy', name: 'DailyReturnDestroy', element: DailyReturnDestroy },
  { path: '/report/inventory', name: 'Inventory', element: Inventory },
  { path: '/report/inventory-listing', name: 'InventoryListing', element: InventoryListing },
  { path: '/report/inventory-received', name: 'InventoryReceived', element: InventoryReceived },
  { path: '/report/daily-activity', name: 'DailyActivity', element: DailyActivity },
  { path: '/report/productivity-report', name: 'ProductivityReport', element: ProductivityReport },
  { path: '/report/input-robot-totals', name: 'InputRobotTotals', element: InputRobotTotals },
]

export default routes



