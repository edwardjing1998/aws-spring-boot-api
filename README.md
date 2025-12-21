import React, { Suspense } from 'react'
import { Routes, Route, Navigate } from 'react-router-dom'
import { CSpinner } from '@coreui/react'

// ✅ Import global styles here
import './scss/style.scss'

// Dashboard
const Dashboard = React.lazy(() => import('./views/dashboard/Dashboard'))
const ArchiveDashboard = React.lazy(() => import('./views/dashboard/ArchiveDashboard'))

// Theme
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

// Forms
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
// Note: 'DeleteCase' imported at top in original file was mapped to DeleteCase component same as here.

const SysPrinConfigs = React.lazy(() => import('./views/rapid-admin-edit/sys-pin-config/SysPrinConfigs'))
const ZipCodeConfig = React.lazy(() => import('./views/rapid-admin-edit/zip-code-config/ZipcodeConfig'))

// Rapid Admin -> Report / Maintenance
const DailyActivity = React.lazy(() => import('./views/rapid-admin-report/daily-activity/DailyActivity'))
const DailyReturnDestroy = React.lazy(() => import('./views/rapid-admin-report/daily-return-destroy/DailyReturnDestroy'))
const Inventory = React.lazy(() => import('./views/rapid-admin-report/inventory/Inventory'))
const InventoryListing = React.lazy(() => import('./views/rapid-admin-report/inventory-listing/InventoryListing'))
const InventoryReceived = React.lazy(() => import('./views/rapid-admin-report/inventory-received/InventoryReceived'))
const ProductivityReport = React.lazy(() => import('./views/rapid-admin-report/productivity/ProductivityReport'))
const InputRobotTotals = React.lazy(() => import('./views/rapid-admin-report/productivity/InputRobotTotals/InputRobotTotals'))
const EmailEventId = React.lazy(() => import('./views/rapid-admin-report/EmailEventId/EmailEventId'))
const AddressChange = React.lazy(() => import('./views/rapid-admin-report/address-change/AddressChange'))

const ClientReportMapping = React.lazy(() => import('./views/rapid-admin-maintenance/client-report-mapping/ClientReportMapping'))
const WebClientDirectory = React.lazy(() => import('./views/rapid-admin-maintenance/web-client-directory/WebClientDirectory'))

const AppRoutes: React.FC = () => {
  return (
    <Suspense
      fallback={
        <div className="pt-3 text-center">
          <CSpinner color="primary" variant="grow" />
        </div>
      }
    >
      <Routes>
        <Route path="/" element={<Navigate to="/dashboard" replace />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/archive-dashboard" element={<ArchiveDashboard />} />
        
        {/* Theme */}
        <Route path="/theme/colors" element={<Colors />} />
        <Route path="/theme/typography" element={<Typography />} />

        {/* Maintenance */}
        <Route path="/maintenance/client-report-mapping" element={<ClientReportMapping />} />
        <Route path="/maintenance/resend-web-reports" element={<Spinners />} />
        <Route path="/maintenance/web-client-directory" element={<WebClientDirectory />} />

        {/* Reports */}
        <Route path="/report/unmatch-sys-prins" element={<ChecksRadios />} />
        <Route path="/report/billing" element={<Toasts />} />
        <Route path="/report/report-queries" element={<Toasts />} />
        <Route path="/report/email-event-id" element={<Toasts />} />
        <Route path="/report/input-rebot-totals" element={<InputRobotTotals />} />
        <Route path="/report/resend-email-reports" element={<Toasts />} />
        <Route path="/report/address-change" element={<AddressChange />} />
        <Route path="/report/mails-with-a-stat" element={<ChecksRadios />} />
        <Route path="/report/status" element={<ChecksRadios />} />
        <Route path="/report/pending-cis" element={<ChecksRadios />} />
        <Route path="/report/failed-non-mons" element={<ChecksRadios />} />
        <Route path="/report/robot-labels" element={<ChecksRadios />} />
        
        <Route path="/report/daily-return-destroy" element={<DailyReturnDestroy />} />
        <Route path="/report/inventory" element={<Inventory />} />
        <Route path="/report/inventory-listing" element={<InventoryListing />} />
        <Route path="/report/inventory-received" element={<InventoryReceived />} />
        <Route path="/report/daily-activity" element={<DailyActivity />} />
        <Route path="/report/productivity-report" element={<ProductivityReport />} />
        <Route path="/report/input-robot-totals" element={<InputRobotTotals />} />

        {/* Query Maintenance */}
        <Route path="/query-maintenance/define-query" element={<ChecksRadios />} />
        <Route path="/query-maintenance/c3-file-transfer" element={<Alerts />} />
        <Route path="/query-maintenance/data-definitions" element={<Badges />} />
        <Route path="/query-maintenance/schedule-batch-report" element={<Range />} />
        <Route path="/query-maintenance/table-load" element={<Toasts />} />
        <Route path="/query-maintenance/table-load-column-mapping" element={<Range />} />
        <Route path="/query-maintenance/tool-tips" element={<Toasts />} />

        {/* Archive Query Maintenance */}
        <Route path="/archive-query-maintenance/c3-file-transfer" element={<ChecksRadios />} />
        <Route path="/archive-query-maintenance/table-load" element={<ChecksRadios />} />
        <Route path="/archive-query-maintenance/tool-tips" element={<Range />} />
        <Route path="/archive-query-maintenance/schedule-batch-report" element={<Range />} />
        <Route path="/archive-query-maintenance/data-definitions" element={<Spinners />} />
        <Route path="/archive-query-maintenance/define-query" element={<Spinners />} />
        <Route path="/archive-query-maintenance/table-load-column-mapping" element={<Spinners />} />

        {/* Archive Reports / Maintenance */}
        <Route path="/archive-maintenance/client-report-mapping" element={<Toasts />} />
        <Route path="/archive-maintenance/resend-web-reports" element={<Spinners />} />
        <Route path="/archive-maintenance/web-client-directory" element={<Tooltips />} />
        <Route path="/archive-report/billing" element={<Alerts />} />
        <Route path="/archive-maintenance/input-robot-totals" element={<Alerts />} />
        <Route path="/archive-report/unmatch-sys-prins" element={<Alerts />} />
        <Route path="/archive-report/report-queries" element={<Alerts />} />
        <Route path="/archive-report/email-event-id" element={<Range />} />

        {/* Components (Base, Buttons, Forms, etc) */}
        <Route path="/base/cards" element={<Cards />} />
        <Route path="/base/accordion" element={<Accordion />} />
        <Route path="/base/breadcrumbs" element={<Breadcrumbs />} />
        <Route path="/base/carousels" element={<Carousels />} />
        <Route path="/base/collapses" element={<Collapses />} />
        <Route path="/base/list-groups" element={<ListGroups />} />
        <Route path="/base/navs" element={<Navs />} />
        <Route path="/base/paginations" element={<Paginations />} />
        <Route path="/base/placeholders" element={<Placeholders />} />
        <Route path="/base/popovers" element={<Popovers />} />
        <Route path="/base/progress" element={<Progress />} />
        <Route path="/base/spinners" element={<Spinners />} />
        <Route path="/base/tabs" element={<Tabs />} />
        <Route path="/base/tables" element={<Tables />} />
        <Route path="/base/tooltips" element={<Tooltips />} />
        
        <Route path="/buttons/buttons" element={<Buttons />} />
        <Route path="/buttons/dropdowns" element={<Dropdowns />} />
        <Route path="/buttons/button-groups" element={<ButtonGroups />} />
        
        <Route path="/charts" element={<Charts />} />
        
        <Route path="/forms/form-control" element={<FormControl />} />
        <Route path="/forms/select" element={<Select />} />
        <Route path="/forms/checks-radios" element={<ChecksRadios />} />
        <Route path="/forms/range" element={<Range />} />
        <Route path="/forms/input-group" element={<InputGroup />} />
        <Route path="/forms/floating-labels" element={<FloatingLabels />} />
        <Route path="/forms/layout" element={<Layout />} />
        <Route path="/forms/validation" element={<Validation />} />

        <Route path="/icons/coreui-icons" element={<CoreUIIcons />} />
        <Route path="/icons/flags" element={<Flags />} />
        <Route path="/icons/brands" element={<Brands />} />

        <Route path="/notifications/alerts" element={<Alerts />} />
        <Route path="/notifications/badges" element={<Badges />} />
        <Route path="/notifications/modals" element={<Modals />} />
        <Route path="/notifications/toasts" element={<Toasts />} />

        <Route path="/widgets" element={<Widgets />} />

        {/* Rapid Admin Edit */}
        <Route path="/edit/global-settings" element={<GlobalSettingForm />} />
        <Route path="/edit/daily-message" element={<DailyMessage />} />
        <Route path="/edit/client-search-input" element={<ClientAutoCompleteInput />} />
        <Route path="/edit/sys-prin-config" element={<SysPrinConfig />} />
        <Route path="/edit/sys-prin-config-new" element={<SysPrinConfigs />} />
        <Route path="/edit/client-information" element={<ClientInformationPanel />} />
        <Route path="/edit/client-information-new" element={<ClientInformationPage />} />
        <Route path="/eidt/receive-files" element={<ReceivingFiles />} />
        <Route path="/edit/email-setup" element={<EmailSetup />} />
        <Route path="/edit/message-table" element={<SysPrinConfig />} />
        <Route path="/edit/zip-code-config" element={<ZipCodeConfig />} />
        <Route path="/edit/mail-type" element={<MailType />} />
        <Route path="/edit/delete-case" element={<DeletedCase />} />
        <Route path="/edit/review-deleted-case" element={<ReviewDeletedCase />} />
        <Route path="/eidt/account-number" element={<SysPrinConfig />} />
      </Routes>
    </Suspense>
  )
}

export default AppRoutes;


AppRoutes.tsx

