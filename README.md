@mixin flex-styles($display, $align, $justify, $wrap) {
  display: $display;
  align-items: $align;
  justify-content: $justify;
  flex-wrap: $wrap;
}

@mixin properties-styles($margin, $width, $padding) {
  margin: $margin;
  width: $width;
  padding: $padding;
}

html {
  overflow: hidden;
}

:root {
  --page-withoutmenu: 1336px;
}

.header {
  @include flex-styles(flex, center, space-between, none);
  height: 64px;
  width: 100%;
  padding: 8px 16px;
  background: #096cd4;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1100;
  box-shadow: 0px 4px 4px 0px rgba(0, 0, 0, 0.25);
}

.leftSection {
  @include flex-styles(flex, center, none, none);
  @include properties-styles(0px, 315.35px, 0);
  height: 40px;
  gap: 5px;
}

.hamburgerbutton {
  @include flex-styles(flex, center, center, none);
  padding: 10px;
  border-radius: 2px;
  background: #096cd4;
}

.hamburger {
  @include properties-styles(0px, 20px, 0);
}

.fiservLogo {
  @include flex-styles(flex, center, center, none);
  gap: 9.408px;
  margin-right: 16px;

}

.stroke {
  @include properties-styles(0px, 0px, 0);
  stroke-width: 1px;
  stroke: #f8f8f8;
  margin: 0 8px;
}

.rapid-layout {
  width: 69px;
  height: 29px;
  left: 39.9px;
  margin-left: 12px;

}

.rapid {
  font-family: 'Baumans';
  font-weight: 400;
  font-size: 30px;
  line-height: 100%;
  letter-spacing: 0%;
  text-transform: lowercase;
  color: #FFFFFF;


}

.admin {
  font-family: 'Open Sans';
  font-weight: 300;
  font-size: 18px;
  line-height: 0px;
  letter-spacing: 0%;
  vertical-align: middle;
  color: #FFFFFF;
  margin-left: 12px;

}

.r-img {
  width: 22.904277801513672;
  height: 26;
  top: 6px;
  left: 1px;
  border-radius: 2.74px;
  border-width: 2px;

}

.rightSection {
  @include flex-styles(flex, center, flex-end, none);
  width: 274px;
  height: 48px;
  gap: 4px;
  display: flex;
  align-items: center;
}

.icon1 {
  @include flex-styles(flex, center, center, none);
  border-radius: 2px;
  background: #096cd4;
  margin: 0 8px;
}

.stroke1 {
  @include properties-styles(1px, 0px, 0);
  margin: 0 8px;
}

@media (max-width: 991.98px) {
  .header {
    padding: 0 8px;
    height: 50px;
    display: flex;
    flex-wrap: nowrap;
  }

  .leftSection .fiservLogo,
  .leftSection .stroke,
  .leftSection .r-img,
  .leftSection .rapid-layout,
  .leftSection .admin {
    display: none;
  }

  .leftSection {
    width: auto !important; 
    min-width: 0;
    flex: 1 1 0%;
  }

  .rightSection {
    width: auto !important; 
    min-width: 0;
    flex: 0 0 auto;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .rightSection .stroke1 {
    display: none;
  }

  .icon1 {
    margin: 0 4px;
  }

  .hamburgerbutton {
    margin-right: 0;
  }
}

@media (max-width: 575.98px) {
  .header {
    height: 44px;
    padding: 0 4px;
  }

  .icon1 svg {
    width: 18px;
    height: 18px;
  }
  .rightSection .stroke1 {
    display: none;
  }
}
