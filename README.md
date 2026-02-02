@use "@coreui/coreui/scss/coreui" as * with (
  $enable-deprecation-messages: false,
);

// ✅ IMPORTANT: everything is scoped under .client-scope
.client-scope {
  // ✅ instead of body (global), apply background on your app root
  background-color: var(--cui-tertiary-bg);
  min-height: 100vh; // make sure it covers the page

  .wrapper {
    width: 100%;
    padding-inline: var(--cui-sidebar-occupy-start, 0) var(--cui-sidebar-occupy-end, 0);
    will-change: auto;
    @include transition(padding .15s);
  }

  .header > .container-fluid,
  .sidebar-header {
    min-height: calc(4rem + 1px);
  }

  .sidebar-brand-full {
    margin-left: 3px;
  }

  .sidebar-header {
    .nav-underline-border {
      --cui-nav-underline-border-link-padding-x: 1rem;
      --cui-nav-underline-border-gap: 0;
    }

    .nav-link {
      display: flex;
      align-items: center;
      min-height: calc(4rem + 1px);
    }
  }

  .sidebar-toggler {
    margin-inline-start: auto;
  }

  .sidebar-narrow,
  .sidebar-narrow-unfoldable:not(:hover) {
    .sidebar-toggler {
      margin-inline-end: auto;
    }
  }

  .header > .container-fluid + .container-fluid {
    min-height: 3rem;
  }

  .footer {
    min-height: calc(3rem + 1px);
  }

  // ✅ dark mode also scoped
  @include color-mode(dark) {
    background-color: var(--cui-dark-bg-subtle);

    .footer {
      --cui-footer-bg: var(--cui-body-bg);
    }
  }
}
