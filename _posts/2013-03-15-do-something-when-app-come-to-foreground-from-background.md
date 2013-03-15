---
layout: default
title: 监听APP从后台(background)切换到前台(foreground) 
---

##监听APP从后台(background)切换到前台(foreground)## 

在`AppDelegate.m`中的`- (void)applicationWillEnterForeground:(UIApplication *)application;`方法在APP即将转到foreground时会被调用。

具体的`UIViewController`中，可以在

    -(void)viewWillAppear:(BOOL)animated;

方法里添加对这一事件的监听：

    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(appWillEnterForegroundNotification)
                                                 name:UIApplicationWillEnterForegroundNotification
                                               object:nil];

`appWillEnterForegroundNotification`即要调用的方法。

最后在`UIViewController`的`-(void)viewWillDisappear:(BOOL)animated`中要移除这个`observer`

    [[NSNotificationCenter defaultCenter] removeObserver:self];
