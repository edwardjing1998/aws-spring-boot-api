html{
  overflow: hidden;
}
:root {
  --page-withoutmenu: 1336px;
}
@mixin flex-styles($display, $alignItems,$justifyContent,$flexDirection){
  display: $display;
  align-items: $alignItems;
  justify-content: $justifyContent;
  flex-direction: $flexDirection;
}
@mixin properties-styles($width,$height,$gap){
width:$width;
height: $height;
gap:$gap
}
@mixin input-styles($border, $outline){
  border:$border;
  outline:$outline;
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
@mixin flex-styles($display, $align-items, $justify-content, $flex-direction) {
  display: $display;
  align-items: $align-items;
  justify-content: $justify-content;
  flex-direction: $flex-direction;
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
