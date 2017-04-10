---
title: "Azure Mobile Engagement Web SDK のアップグレード手順 | Microsoft Docs"
description: "Web SDK for Azure Mobile Engagement の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326


---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure Mobile Engagement Web SDK のアップグレード手順
以前のバージョンの Azure Mobile Engagement Web SDK を既に Web アプリケーションに統合している場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

Mobile Engagement Web SDK の一部のバージョンが不足している場合、アップグレード プロセス中に手順をいくつか完了しなければならないことがあります。 たとえば、1.4.0 から 1.6.0 に移行する場合は、1.4.0 から 1.5.0 にアップグレードしたうえで、 1.5.0 から 1.6.0 にアップグレードする手順に従います。

どのバージョンからアップグレードする場合でも、以前のバージョンの azure-engagement.js ファイルはすべて、最新バージョンのファイルに置き換えてください。

## <a name="upgrade-from-121-to-200"></a>1.2.1 から 2.0.0 へのアップグレード
このセクションでは、Mobile Engagement Web SDK 統合を、Capptain SAS によって提供される Capptain サービスから、Azure Mobile Engagement アプリに移行する方法について説明します。 以前のバージョンから移行する場合は、Capptain Web サイトをご覧のうえ、まず 1.2.1 に移行してから、次の手順を適用してください。

このバージョンの Mobile Engagement Web SDK では、Samsung Smart TV、Opera TV、webOS、または Reach の機能がサポートされません。

> [!IMPORTANT]
> Capptain と Azure Mobile Engagement は、同じサービスではありません。 次の手順では、クライアント アプリを移行する方法にのみ焦点を当てています。 アプリで Mobile Engagement Web SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。
> 
> 

### <a name="javascript-files"></a>JavaScript ファイル
capptain-sdk.js ファイルを azure-engagement.js ファイルに置き換え、スクリプトのインポートを適宜更新します。

### <a name="remove-capptain-reach"></a>Capptain Reach の削除
このバージョンの Mobile Engagement Web SDK では、Reach の機能がサポートされません。 アプリケーションに Capptain Reach を統合した場合は、それを削除する必要があります。

Reach CSS のインポートをページから削除し、関連する css ファイル (既定では capptain-reach.css) を削除します。

Reach のリソースである終了画像 (既定では capptain-close.png) とブランド アイコン (既定では capptain-notification-icon) を削除してください。

アプリ内通知の Reach UI を削除します。 既定のレイアウトは次のとおりです。

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

テキスト/Web 通知および投票に使用される Reach UI を削除します。 既定のレイアウトは次のとおりです。

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

`reach` オブジェクトを構成から削除します (存在する場合)。 次のように表示されています。

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

その他の Reach 関連のカスタマイズ (カテゴリなど) をすべて削除します。

### <a name="remove-deprecated-apis"></a>非推奨 API の削除
Capptain の API の中には、Mobile Engagement Web SDK では非推奨になっているものがあります。

`agent.connect`、`agent.disconnect`、`agent.pause`、`agent.sendMessageToDevice` の各 API の呼び出しをすべて削除します。

Capptain の構成から `onConnected`、`onDisconnected`、`onDeviceMessageReceived`、`onPushMessageReceived` コールバックのインスタンスをすべて削除します。

### <a name="configuration"></a>構成
Mobile Engagement では、接続文字列を使用して、アプリケーション ID などの SDK 識別子を構成します。

アプリケーション ID を接続文字列に置き換えます。 SDK 構成のグローバル オブジェクトが、`capptain` から `azureEngagement` に変わっている点に注意してください。

移行前:

    window.capptain = {
      appId: ...,
      [...]
    };

移行後:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

アプリケーションの接続文字列が Azure ポータルに表示されます。

### <a name="javascript-apis"></a>JavaScript API
グローバル JavaScript オブジェクト `window.capptain` の名前が `window.azureEngagement` に変更されましたが、API 呼び出しには `window.engagement` エイリアスを使用できます。 そのエイリアスを使用して、SDK の構成を定義することはできません。

たとえば、`capptain.deviceId` は `engagement.deviceId` になり、`capptain.agent.startActivity` は `engagement.agent.startActivity` になります。他も同様です。




<!--HONumber=Nov16_HO3-->


