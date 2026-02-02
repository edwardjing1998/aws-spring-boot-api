/* =========================================================
   Scope Root
   - Put this class on your app root container, or a page wrapper
   - Example: <div className="reactui-scope"> ... </div>
========================================================= */
.reactui-scope {
  /* local token (won’t pollute global :root) */
  --page-withoutmenu: 1336px;

  /* Optional: if you need to lock scroll ONLY for this scope */
  overflow: hidden;

  /* -----------------------------
     Mixins (same standard)
  ------------------------------ */
  @mixin flex-styles($display, $alignItems, $justifyContent, $flexDirection) {
    display: $display;
    align-items: $alignItems;
    justify-content: $justifyContent;
    flex-direction: $flexDirection;
  }

  @mixin properties-styles($width, $height, $gap) {
    width: $width;
    height: $height;
    gap: $gap;
  }

  @mixin input-styles($border, $outline) {
    border: $border;
    outline: $outline;
  }

  @mixin border-radius($radius) {
    border-radius: $radius;
  }

  @mixin box-size($width, $height) {
    width: $width;
    height: $height;
  }

  @mixin font-style($font-weight, $font-size, $line-height) {
    font-weight: $font-weight;
    font-size: $font-size;
    line-height: $line-height;
  }

  @mixin button-styles($background-color, $font-color) {
    background: $background-color;
    color: $font-color;
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
  }

  @mixin margin($margin-top, $margin-right, $margin-bottom, $margin-left) {
    margin-top: $margin-top;
    margin-right: $margin-right;
    margin-bottom: $margin-bottom;
    margin-left: $margin-left;
  }

  /* keep your duplicate definition style if you want;
     but make it identical to avoid unexpected overrides */
  @mixin flex-styles($display, $alignItems, $justifyContent, $flexDirection) {
    display: $display;
    align-items: $alignItems;
    justify-content: $justifyContent;
    flex-direction: $flexDirection;
  }

  @mixin border-radius($radius) {
    border-radius: $radius;
  }

  @mixin padding($padding-top, $padding-right, $padding-bottom, $padding-left) {
    padding-top: $padding-top;
    padding-right: $padding-right;
    padding-bottom: $padding-bottom;
    padding-left: $padding-left;
  }
}

/* =========================================================
   OPTIONAL (recommended):
   If you previously relied on html { overflow:hidden; }
   replace it with a body class you toggle in React.
========================================================= */

/* When you add class="reactui-noscroll" on body, it locks scroll safely */
body.reactui-noscroll {
  overflow: hidden;
}
