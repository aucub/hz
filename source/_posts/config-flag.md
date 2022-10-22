---
title: Firefox，Edge高级设置
keywords: 'edge://flags,about:config'
abbrlink: 908ef1c
date: 2022-10-22 11:21:21
updated: 2022-10-22 11:21:21
tags:
---
Firefox部分about:config设置，Edge部分flags特性设置
<!-- more -->
## Firefox部分about:config选项设置  

双击关闭标签页（默认为True）`browser.tabs.closeTabByDblclick`

支持多标签页管理，使用 Ctrl 和 Shift控制`browser.tabs.multiselect`

同步所有首选项`services.sync.prefs.dangerously_allow_arbitrary`

设置内置页面暗色调`browser.in-content.dark-mode`

设置外观为暗色，新建选项 `ui.systemUsesDarkTheme` 为1

为隐私模式单独设置搜索引擎`browser.search.separatePrivateDefault.ui.enabled`

搜索高亮强调动画`findbar.modalHighlight`

单击鼠标中建粘贴剪切板内容`middlemouse.paste`

右键保存到 pocket（设置False隐藏）`extensions.pocket.enabled`

数据收集 `about:telemetry`  详情访问[about:telemetry](about:telemetry)

发现`browser.discovery.enabled`

同步设备名`identity.fxaccounts.account.device.name`

列出所有标签页按钮常驻`browser.tabs.tabmanager.enabled`

设置书签在新标签页中打开`browser.tabs.loadBookmarksInTabs`  

在 Firefox 官方界面使用插件`extensions.webextensions.restrictedDomains`

禁用WebRTC将其设置为“false”`media.peerconnection.enabled`

设置为 false可解决部分风险网站无法访问`browser.safebrowsing.malware.enabled`

滚动条大小`widget.non-native-theme.scrollbar.style`

改成true加载自定义css样式`toolkit.legacyUserProfileCustomizations.stylesheets`

设置产品推广（可关闭设置页APP推广）`browser.preferences.moreFromMozilla`

显示拦截弹窗提示`privacy.popups.showBrowserMessage`

调成ture防止假域名视觉欺骗`network.IDN_show_punycode`

搜索引擎固定建议`browser.newtabpage.activity-stream.improvesearch.topSiteSearchShortcuts.havePinned`

同上`browser.newtabpage.activity-stream.improvesearch.topSiteSearchShortcuts.searchEngines`  

## Edge部分flags特性设置  

### Edge桌面版部分flags特性设置  

流畅滑动`edge://flags/#smooth-scrolling`

quic启用`edge://flags/#enable-quic`

并行下载`edge://flags/#enable-parallel-downloading`

https自动升级`edge://flags/#edge-automatic-https`

实验性外观`edge://flags/#edge-visual-rejuv-show-settings`

优化字体显示`edge://flags/#edge-enhance-text-contrast`

悬浮滚动条`edge://flags/#edge-overlay-scrollbars-win-style`

菜单亚克力效果`edge://flags/#edge-visual-rejuv-materials-menu`  

### Edge安卓版部分flags特性设置 

流畅滑动`edge://flags/#smooth-scrolling`

quic启用`edge://flags/#enable-quic`

懒加载`edge://flags/#enable-webassembly-lazy-compilation`

vulkan启用`edge://flags/#enable-vulka`

并行下载`edge://flags/#enable-parallel-downloading`

阻止自动播放设置`edge://flags/#edge-autoplay-user-setting-block-option`

开启广告手动标记选项`edge://flags/#edge-tag-ads`

手动标记广告即时关闭`edge://flags/#edge-tag-ads-silent`

返回，前进不重载缓存`edge://flags/#back-forward-cache`

https自动升级`edge://flags/#edge-automatic-https`

写作助手`edge://flags/#edge-writing-assistant-android`