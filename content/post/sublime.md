---
title: sublime
author: Bhblinux
tags:
  - sublime
categories:
  - 记事本
date: 2018-06-19 20:27:00
---

## sublime
ctrl+~
复制
 import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())

package control 官网推荐
 import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

sublime 3176 激活
1. 修改hosts
127.0.0.1       www.sublimetext.com
127.0.0.1       license.sublimehq.com
2. 激活码
----- BEGIN LICENSE -----
sgbteam
Single User License
EA7E-1153259
8891CBB9 F1513E4F 1A3405C1 A865D53F
115F202E 7B91AB2D 0D2A40ED 352B269B
76E84F0B CD69BFC7 59F2DFEF E267328F
215652A3 E88F9D8F 4C38E3BA 5B2DAAE4
969624E7 DC9CD4D5 717FB40C 1B9738CF
20B3C4F1 E917B5B3 87C38D9C ACCE7DD8
5F7EF854 86B9743C FADC04AA FB0DA5C0
F913BE58 42FEA319 F954EFDD AE881E0B
------ END LICENSE ------



然后
ctrl+shift+p 安装插件
搜索chinese关键词汉化
2. 修改字体
"font_face": "Inconsolata",
    "font_size": 12,
  复制到最上保存没有报错即可

关闭自动更新
"update_check":false,
 设置右键快捷菜单
regeidt
HKEY_CLASSES_ROOT>*>shell
新建项
sublime
>command
>>新建字符串 
"C:\Program Files\Sublime Text 3\sublime_text.exe" -p --remote-tab-silent  "%1"
配置vim
VintageEx 插件

  "ignored_packages": ["Vintage"] 将Vintage去掉即可
然后安装Vintageous 插件,3版本 


1.vim编辑器的模式
1.命令模式
2.输入模式
3.莫行模式


配置sublime,vim模式为命令模式修改
"vintage_start_in_command_mode": true


关闭自动补全
    "auto_match_enabled": false,
   关闭命令补全
      "auto_complete": false,
背景线
 "highlight_line": true,
 切换注释
 ctrl+/
