---
title: "vscode个人配置文件"
date: 2024-10-21T21:52:50.509700
categories: ['Vscode']
author: "Ronan"
---
> [!note]
>
> 以下为个人自用配置文件，如需拷贝使用请把 **插件配置区** 的选项删除或者安装相应的插件。

```

{
    //插件配置区*****************************************************************************************

    //vscode animation 搭配 Apc Customize UI++ 实现果冻光标
    "animations.Install-Method": "Apc Customize UI++",
    "apc.imports": [
        "file://${userHome}/.vscode/extensions/brandonkirbyson.vscode-animations-2.0.3/dist/updateHandler.js"
    ],
    "animations.CursorAnimation": true,
    "animations.CursorAnimationOptions": {
        "Color": "#ffb6c1",
        "TrailLength": 8
    },
    "animations.Smooth-Mode": false,

    // livliveServer插件
    "liveServer.settings.donotShowInfoMsg": true,


    //编辑器系统设置配置区*****************************************************************************************


    // 平滑动画
    "editor.cursorSmoothCaretAnimation": "on",

    //设置光标的闪烁样式
    "editor.cursorBlinking": "smooth",

    //是否启用平滑滚动。设为 true 时，滚动编辑器内容会有平滑的动画效果。默认值为 false。
    "editor.smoothScrolling": true,

    //是否自动隐藏缩略图
    "editor.minimap.autohide": true,

    //字体大小
    "editor.fontSize": 13,

    //光标样式
    "editor.cursorStyle": "block",

    //控制资源管理器是否应在通过回收站删除文件时要求确认。
    "explorer.confirmDelete": false,

    //控制在粘贴本机文件和文件夹时资源管理器是否应要求进行确认。
    "explorer.confirmPasteNative": false,

    // 自动重命名配对的HTML/XML标签
    "editor.linkedEditing": true,

    //控制是否启用水平括号对指南。
    "editor.guides.bracketPairsHorizontal": true,

    // 括号对参考线
    "editor.guides.bracketPairs": true,

    // 控制每个方括号类型是否具有自己的独立颜色池。
    "editor.bracketPairColorization.independentColorPoolPerBracketType": true,

    // 文件自动保存
    "files.autoSave": "afterDelay",

    // 启用后，将在保存文件时删除行尾的空格。
    "files.trimTrailingWhitespace": true,

    // html标签自动关闭
    "html.autoClosingTags": true,

    // js标签自动关闭
    "javascript.autoClosingTags": true,

    //typescript标签自动关闭
    "typescript.autoClosingTags": true,

    //终端光标闪烁
    "terminal.integrated.cursorBlinking": true,

    //控制是否将终端中选择的文本复制到剪贴板。
    "terminal.integrated.copyOnSelection": true,

    //默认终端
    "terminal.integrated.defaultProfile.osx": "zsh",

    //终端字体大小
    "terminal.integrated.fontSize": 13,

    //改变活动栏（Activity Bar）的显示位置。
    "workbench.activityBar.location": "bottom",

    //主题颜色
    "workbench.colorTheme": "One Dark Pro Flat",

    //资源管理器文件夹层级缩进大小
    "workbench.tree.indent": 24,

    // "One Dark Pro Flat"括号对颜色
    // 如更换其他主题，请更换中括号里的主题名
    "workbench.colorCustomizations": {

        //光标颜色
        "editorCursor.foreground": "#ffb6c1",

        "[One Dark Pro Flat]":{
            "editorBracketHighlight.foreground1":"#e78009",
            "editorBracketHighlight.foreground2":"#22990a",
            "editorBracketHighlight.foreground3":"#ffffff",
            "editorBracketHighlight.foreground4":"#ddcf11",
            "editorBracketHighlight.foreground5":"#9c15c5",
            "editorBracketHighlight.foreground6":"#1411c4",
            "editorBracketHighlight.unexpectedBracket.foreground":"#FF2C6D"
        }
    },

    //----------------------------------------------------------------------------------------

    //在此数组中添加的每一个设置项的键名，将在所有配置文件中保持相同的值。
    //用来配置在所有配置文件（profiles）中应用的设置项
    //相当于全局应用
    "workbench.settings.applyToAllProfiles": [
        // 光标平滑插入动画
        "editor.cursorSmoothCaretAnimation",
        // 光标闪烁
        "editor.cursorBlinking",
    ],
    "terminal.integrated.inheritEnv": false,
}
``` 
