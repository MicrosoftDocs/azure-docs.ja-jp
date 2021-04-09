---
title: ログの記録と診断
description: Azure Spatial Anchors でログ記録と診断を生成して取得する方法に関する、詳細な説明。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95494643"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure Spatial Anchors でのログ記録と診断

Azure Spatial Anchors は、アプリの開発に役立つ標準のログ記録メカニズムを提供します。 Spatial Anchors の診断ログ モードは、デバッグに関してより多くの情報が必要な場合に役立ちます。 診断ログは、環境のイメージを保存します。

## <a name="standard-logging"></a>標準的なログ記録
Spatial Anchors API では、ログ記録メカニズムをサブスクライブすることで、アプリケーション開発とデバッグに役立つログを取得することができます。 標準的なログ記録 API では、デバイス ディスク上に環境の画像は保存されません。 これらのログは SDK でイベントのコールバックとして提供されます。 これらのログをアプリケーションのログ記録メカニズムに統合するかどうかは、お客様が選ぶことができます。

### <a name="configuration-of-log-messages"></a>ログ メッセージの構成
ユーザーが関心を持つコールバックが 2 つあります。 次のサンプルは、セッションの構成方法を示しています。

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>イベントとプロパティ

これらのイベント コールバックは、ログとエラーをセッションから処理するために提供されています。

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): ランタイムから受信するイベントの詳細のレベルを指定します。
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): 標準的なデバッグ ログ イベントが提供されます。
- [Error](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): ランタイムでエラーであると見なされたログ イベントが提供されます。

## <a name="diagnostics-logging"></a>診断ログ

Spatial Anchors にはログ記録操作の標準モードに加え、診断モードも存在します。 診断モードでは、環境のイメージがキャプチャされ、ディスクにログ記録されます。 このモードを使用すると、アンカーの予測検索の失敗など、特定の種類の問題をデバッグすることができます。 診断ログは、特定の問題を再現するためにのみ、有効にしてください。 終了後は、無効にします。 アプリの通常実行時には、診断を有効にしないでください。

Microsoft サポートの利用中に、Microsoft の担当者が詳しい調査のため診断バンドルの提出を依頼することがあります。 この場合、診断を有効にして問題を再現し、診断バンドルを送信することができます。

Microsoft の担当者が事前に認識していない状態で診断ログを Microsoft に提出しても、返答は得られません。

以下のセクションでは、診断モードを有効にする方法と、Microsoft に診断ログを送信する方法についても説明します。

### <a name="enable-diagnostics-logging"></a>診断ログの有効化

セッションについて診断ログを有効にすると、セッション内のすべての操作に対応する診断ログがローカル ファイル システムに生成されます。 ログ記録の間、環境のイメージがディスクに保存されます。

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>診断バンドルの提出

次のコード スニペットは、診断バンドルを Microsoft に提出する方法を示しています。 このバンドルには、診断を有効にした後にセッションでキャプチャされた環境のイメージが含まれます。

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>診断バンドルのパーツ
診断バンドルには次のような情報が含まれています。

- **キーフレーム イメージ**: 診断が有効な状態のときにセッションでキャプチャされた環境のイメージ。
- **ログ**: ランタイムによって記録されたログ イベント。
- **セッション メタデータ**: セッションを識別するメタデータ。