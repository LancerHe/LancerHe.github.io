---
title: Javascript获取远程图片宽高
author: 谇雨
layout: post
permalink: /javascript-get-remote-image-height-width.html
categories:
  - 前端设计
tags:
  - javascript
  - 远程图片
---

    function getMeta(varA, varB) {
        if (typeof varB !== 'undefined') {
            alert(varA + ' width ' + varB + ' height');
        } else {
            var img = new Image();
            img.src = varA;
            img.onload = function() {
                getMeta(this.width, this.height);
            }
        }
    }

    getMeta("http://snook.ca/files/mootools_83_snookca.png");