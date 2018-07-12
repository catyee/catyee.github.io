---
title: css3 transitions，transforms和annimation的总结
date: 2018-07-09 08:47:43
tags: css
description: 在写css3时经常会混淆transitions，transforms和annimation这三个属性，所以还是很有必要总结一下的(●'◡'●)
categories: 总结(css)
---
css3 transitions，transforms和annimation的总结
transition
transition(过渡):  background-color（要过渡的属性）0.3s(过渡的持续时间) 0.1s(过渡的延长时间) ease | linear(线性过渡) | ease-in（由慢到快） | ease-out（由快到慢） | ease-in-out（由慢到快再到慢） | cubic-bezier()（贝塞尔曲线）
例如：
```
        
    .trans {
        -webkit-transition-property: background-color;
        -webkit-transition-duration: 0.3s;
        -webkit-transition-timing-function: ease;
    }
    .trans:hover {
        background-color: #486AAA;
        color: #fff;
    }

    transform
    transform(变换)： skew(35deg); （倾斜35deg）
    transform: scale(1,0.5); (x方向不变，y方向缩小0.5)
    transform: rotate(30deg) （旋转45deg）
    transform: translate(10px,20px)(x方向平移10px，y方向平移20px)
```

transform可以跟transition结合使用，能够使变化效果更平滑，例如：

```
    .trans_effect {
    -webkit-transition:all 2s ease-in-out;
    -moz-transition:all 2s ease-in-out;
    -o-transition:all 2s ease-in-out;
    -ms-transition:all 2s ease-in-out;    
    transition:all 2s ease-in-out;
    }
    .trans_effect:hover {
        -webkit-transform:rotate(720deg) scale(2,2);
        -moz-transform:rotate(720deg) scale(2,2);
        -o-transform:rotate(720deg) scale(2,2);
        -ms-transform:rotate(720deg) scale(2,2);
        transform:rotate(720deg) scale(2,2);        
    }
```

animations
实现鼠标移入外发光

```
    @-webkit-keyframes glow {
        0% {
            -webkit-box-shadow: 0 0 12px rgba(72, 106, 170, 0.5);
            border-color: rgba(160, 179, 214, 0.5);         
        }
        100% {
            -webkit-box-shadow: 0 0 12px rgba(72, 106, 170, 1.0), 0 0 18px rgba(0, 140, 255, 1.0);
            border-color: rgba(160, 179, 214, 1.0); 
        }
    }
    .anim_image {
        padding:3px;
        border:1px solid #beceeb;
        background-color:white;
        -moz-box-shadow: 0 0 8px rgba(72, 106, 170, 0.5);
        -webkit-box-shadow: 0 0 8px rgba(72, 106, 170, 0.5);
        box-shadow: 0 0 8px rgba(72, 106, 170, 0.5);
    }
    .anim_image:hover {
        background-color:#f0f3f9;
        -webkit-animation-name: glow;
        -webkit-animation-duration: 1s;
        -webkit-animation-iteration-count: infinite;
        -webkit-animation-direction: alternate;
        -webkit-animation-timing-function: ease-in-out;    
    }
```



















