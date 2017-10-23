---
title: css预编译工具-sass
tags:
     - sass
---
记录sass使用的日常....

<!--more-->

## 常用mixin
``` bash
@mixin font-dpr($font-size) {
  font-size: $font-size;
  [data-dpr="2"] & {
    font-size: $font-size * 2;
  }
  [data-dpr="3"] & {
    font-size: $font-size * 3;
  }
}

$baseFontSize: 100;
$FontSize: 16;
@mixin px2rem($name, $px1){
  #{$name}: ($px1 / $baseFontSize) * 1rem;
}

@mixin bg-image($url,$type) {
  background-image: url($url + "@2x."+$type);
  @media (-webkit-min-device-pixel-ratio: 3), (min-device-pixel-ratio: 3) {
    background-image: url($url + "@3x."+$type);
  }
}

@mixin clearfix() {
    &:before,
    &:after {
        content: "";
        display: table;
    }
    &:after {
        clear: both;
    }
}

@mixin remCalc($props,$sizes,$base:$baseFontSize){
 $values: ();
 $sublists: false;
  @each $s in $sizes {
     @if type-of($s) == list {
       $sublists: true; $vv: ();
        @each $ss in $s {
           $vv: append($vv,if(type-of($ss) == number, #{$ss / $base}rem, $ss));
         }
         $values: append($values,join((), $vv));
       } @else {
          $values: append($values,if(type-of($s) == number, #{$s / $base}rem, $s));
        }
    }
  $value: join((), $values, if($sublists,comma,space));
  @each $prop in $props {#{$prop}: $value}
}

@mixin css3($property, $value) {
    @each $prefix in -webkit-,
    -moz-,
    -ms-,
    -o-,
    '' {
        #{$prefix}#{$property}: ($value / $baseFontSize) * 1rem;
    }
}

@mixin css3fn($property, $value) {
    @each $prefix in -webkit-,
    -moz-,
    -ms-,
    -o-,
    '' {
        #{$prefix}#{$property}: $value;
    }
}
```