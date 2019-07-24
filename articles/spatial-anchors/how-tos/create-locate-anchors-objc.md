---
title: Objective-C で Azure Spatial Anchors を使用してアンカーを作成して配置する方法 | Microsoft Docs
description: Objective-C で Azure Spatial Anchors を使用してアンカーを作成して配置する方法の詳細な説明。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3dd4fbedd51e11e84303f997cef0bb28bf79b241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918180"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-objective-c"></a>Objective-C で Azure Spatial Anchors を使用してアンカーを作成して配置する方法

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors を使用して、世界中の異なるデバイス間でアンカーを共有できます。 これは複数の異なる開発環境をサポートしています。 この記事では、Azure Spatial Anchors SDK を Objective-C で使用する方法について説明します。次のことを行います。

- Azure Spatial Anchors セッションを正しく設定して管理する。
- ローカル アンカーのプロパティを作成して設定する。
- それらをクラウドにアップロードする。
- クラウドの空間アンカーを検索して削除する。

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、以下が必要です。

- 「[Azure Spatial Anchors の概要](../overview.md)」を読んでいる。
- [5 分間のクイック スタート](../index.yml)のいずれかを完了している。
- Objective-C に関する基本的な知識。
- <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> についての基本的な知識。

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

[ASACloudSpatialAnchorSession](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession) クラスの詳細を確認してください。

```objc
    ASACloudSpatialAnchorSession *_cloudSession;
    // In your view handler
    _cloudSession = [[ASACloudSpatialAnchorSession alloc] init];
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

[ASASessionConfiguration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration) クラスの詳細を確認してください。

```objc
    _cloudSession.configuration.accountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```objc
    _cloudSession.configuration.accessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

[tokenRequired](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) プロトコル メソッドの詳細を確認してください。

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.accessToken = @"MyAccessToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.accessToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```objc
    _cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.authenticationToken = @"MyAuthenticationToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.authenticationToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

[start](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) メソッドの詳細を確認してください。

```objc0
    _cloudSession.session = self.sceneView.session;
    _cloudSession.delegate = self;
    [_cloudSession start];
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

[processFrame](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe) メソッドの詳細を確認してください。

```objc
    [_cloudSession processFrame:_sceneView.session.currentFrame];
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

[sessionUpdated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) プロトコル メソッドの詳細を参照してください。

```objc
    - (void)sessionUpdated:(ASACloudSpatialAnchorSession *)cloudSession :(ASASessionUpdatedEventArgs *)args {
        ASASessionStatus *status = [args status];
        if (status.userFeedback == ASASessionUserFeedbackNone) return;
        _feedback = [NSString
            stringWithFormat:@"Feedback: %@ - Recommend Create=%.0f%%",
            FeedbackToString(status.userFeedback),
            status.recommendedForCreateProgress * 100.f];
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

[ASACloudSpatialAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor) クラスの詳細を確認してください。

```objc
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    NSArray<ARHitTestResult *> *hits = [_sceneView.session.currentFrame hitTest:CGPointMake(0.5, 0.5) types:ARHitTestResultTypeEstimatedHorizontalPlane];
    if ([hits count] == 0) return;
    // The hitTest method sorts the resulting list by distance from the camera, increasing
    // The first hit result will usually be the most relevant when responding to user input
    ARAnchor *localAnchor = [[ARAnchor alloc] initWithTransform:hits[0].worldTransform];
    [_sceneView.session addAnchor:localAnchor];

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Save Failed:%@", error.localizedDescription];
            return;
        }
        _feedback = [NSString stringWithFormat:@"Created a cloud anchor with ID=%@", cloudAnchor.identifier];
    }];
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

[getSessionStatusWithCompletionHandler](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus) メソッドの詳細を参照してください。

```objc
    [_cloudSession getSessionStatusWithCompletionHandler:^(ASASessionStatus *value, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Session status error:%@", error.localizedDescription];
            return;
        }
        if (value.recommendedForCreateProgress < 1.0f) return;
        // Issue the creation request ...
    }];
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

[appProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) プロパティの詳細を確認してください。

```objc
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    cloudAnchor.appProperties = @{ @"model-type" : @"frame", @"label" : @"my latest picture" };
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // ...
    });
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

[updateAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties) メソッドの詳細を確認してください。

```objc
    ASACloudSpatialAnchor *anchor = /* locate your anchor */;
    [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
    [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
        if (error) _feedback = [NSString stringWithFormat:@"Updating Properties Failed:%@", error.localizedDescription];
    }];
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

[getAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties) メソッドの詳細を参照してください。

```objc
    [_cloudSession getAnchorProperties:@"anchorId" withCompletionHandler:^(SCCCloudSpatialAnchor *anchor, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Getting Properties Failed:%@", error.localizedDescription];
            return;
        }
        if (anchor) {
            [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
            [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
                // ...
            }];
        }
    }];
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

[expiration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) プロパティの詳細を確認してください。

```objc
    int secondsInAWeek = 60 * 60 * 24 * 7;
    NSDate *oneWeekFromNow = [[NSDate alloc] initWithTimeIntervalSinceNow: (NSTimeInterval) secondsInAWeek];
    cloudAnchor.expiration = oneWeekFromNow;
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

[createWatcher](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher) メソッドの詳細を確認してください。

```objc
    ASAAnchorLocateCriteria *criteria = [ASAAnchorLocateCriteria new];
    criteria.identifiers = @[ @"id1", @"id2", @"id3" ];
    [_cloudSession createWatcher:criteria];
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[anchorLocated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) プロトコル メソッドの詳細を確認してください。

```objc
    - (void)anchorLocated:(ASACloudSpatialAnchorSession *)cloudSession :(ASAAnchorLocatedEventArgs *)args {
        ASALocateAnchorStatus status = [args status];
        switch (status) {
        case ASALocateAnchorStatusLocated: {
            ASACloudSpatialAnchor *foundAnchor = [args anchor];
            // Go add your anchor to the scene...
        }
            break;
        case ASALocateAnchorStatusAlreadyTracked:
            // This anchor has already been reported and is being tracked
            break;
        case ASALocateAnchorStatusNotLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break;
        case ASALocateAnchorStatusNotLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break;
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

[deleteAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor) メソッドの詳細を確認してください。

```objc
    [_cloudSession deleteAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // Perform any processing you may want when delete finishes
    }];
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

[stop](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) メソッドの詳細を確認してください。

```objc
    [_cloudSession stop];
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

[reset](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) メソッドの詳細を確認してください。

```objc
    [_cloudSession reset];
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```objc
    _cloudSession = NULL;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
