---
title: Azure CDN のリアルタイム アラート | Microsoft Docs
description: Microsoft Azure CDN のリアルタイム アラート。 リアルタイム アラートは、CDN プロファイル内のエンドポイントのパフォーマンスに関する通知を提供します。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887244"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Microsoft Azure CDN のリアルタイム アラート
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概要
このドキュメントでは、Microsoft Azure CDN のリアルタイム アラートについて説明します。 この機能は、CDN プロファイル内のエンドポイントのパフォーマンスに関するリアルタイムの通知を提供します。  次の条件に基づいて、電子メール アラートまたは HTTP アラートを設定することができます。

* 帯域幅
* 状態コード
* キャッシュの状態
* 接続

## <a name="creating-a-real-time-alert"></a>リアルタイム アラートの作成
1. [Azure Portal](https://portal.azure.com) で、CDN プロファイルに移動します。
   
    ![CDN プロファイル](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. CDN プロファイル ブレードで、 **[管理]** をクリックします。
   
    ![[CDN プロファイル] の [管理] ボタン](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
3. **[分析]** タブにマウス ポインターを合わせ、次に **[Real-Time Stats (リアルタイム統計情報)]** フライアウトにポインターを合わせます。  **[Real-Time Alerts (リアルタイム アラート)]** をクリックします。
   
    ![CDN management portal](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    既存のアラートの構成の一覧が表示されます (ある場合)。
4. **[アラートの追加]** ボタンをクリックします。
   
    ![[アラートの追加] ボタン](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    新しいアラートを作成するためのフォームが表示されます。
   
    ![[新しい警告] フォーム](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. **[保存]** をクリックしたときにこのアラートをアクティブにする場合は、**[有効な警告]** チェックボックスをオンします。
6. **[名前]** フィールドにアラートのわかりやすい名前を入力します。
7. **[Media Type (メディアの種類)]** ドロップダウンで、**[HTTP Large Object (HTTP ラージ オブジェクト)]** を選択します。
   
    ![[HTTP Large Object (HTTP ラージ オブジェクト] を選択した [Media Type (メディアの種類)]](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > **メディアの種類** として **[HTTP Large Object (HTTP ラージ オブジェクト)]** を選択する必要があります。  **Azure CDN from Verizon** では、他の選択肢は使用しません。  **[HTTP Large Object]\(HTTP ラージ オブジェクト\)** を選択しないと、アラートはトリガーされません。
   > 
   > 
8. **[メトリック]**、**[演算子]**、および **[トリガー値]** を選択して、監視する **式** を作成します。
   
   * **[メトリック]** では、監視条件の種類を選択します。  **[Bandwidth Mbps (帯域幅 Mbps)]** は、メガビット/秒単位の使用帯域幅の量です。  **[合計接続数]** は、エッジ サーバーへの同時 HTTP 接続の数です。  さまざまなキャッシュ ステータスとステータス コードの定義については、「[Azure CDN Cache Status Codes (Azure CDN のキャッシュ ステータス コード)](/previous-versions/azure/mt759237(v=azure.100))」と「[Azure CDN HTTP Status Codes (Azure CDN の HTTP ステータス コード)](/previous-versions/azure/mt759238(v=azure.100))」を参照してください
   * **[演算子]** は、メトリックとトリガー値の間のリレーションシップを確立する算術演算子です。
   * **[トリガー値]** は、通知の送信前に満たす必要があるしきい値です。
     
     次の例で作成される式は、404 状態コードの数が 25 を超えると通知が送信されることを示しています。
     
     ![リアルタイム アラートのサンプル式](./media/cdn-real-time-alerts/cdn-expression.png)
9. **[間隔]** に、式を評価する頻度を入力します。
10. **[Notify on (通知タイミング)]** ドロップダウンで、式が true のときに通知を行うタイミングを選択します。
    
    * **[Condition Start]\(条件開始時\)** は、指定された条件を最初に検出したときに通知を送信することを示します。
    * **[Condition End]\(条件終了時\)** は、指定された条件を検出しなくなったときに通知を送信することを示します。 この通知は、ネットワーク監視システムが、指定された条件の発生を検出した後にのみトリガーすることができます。
    * **[継続]** は、ネットワーク監視システムが指定された条件を検出するたびに通知を送信することを示します。 ネットワーク監視システムは、間隔 1 回ごとに 1 回しか指定された条件をチェックしないことに注意してください。
    * **[Condition Start and End]\(条件の開始時と終了時\)** は、指定された条件を最初に検出したときと、条件が検出されなくなったときに、通知を送信することを示します。
1. 電子メールで通知を受信する場合は、 **[Notify by Email (電子メールで通知)]** チェックボックスをオンします。  
    
    ![[Notify by Email (電子メールで通知)] フォーム](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    **[To (宛先)]** フィールドに、通知の送信先とする電子メール アドレスを入力します。 **[件名]** と **[本文]** は、既定のままにすることも、**[Available keywords (使用可能なキーワード)]** ボックスの一覧を使ってメッセージをカスタマイズし、メッセージ送信時にアラート データを動的に挿入することもできます。
    
    > [!NOTE]
    > **[テスト通知]** ボタンをクリックすることで電子メール通知をテストできますが、このテストはアラートの構成を保存した後にのみ実行できます。
    > 
    > 
12. 通知を Web サーバーにポストする場合は、 **[Notify by HTTP Post (HTTP Post で通知)]** チェックボックスをオンします。
    
    ![[Notify by HTTP Post (HTTP Post で通知)] フォーム](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    **[URL]** フィールドに、HTTP メッセージをポストする URL を入力します。 **[ヘッダー]** テキストボックスに、要求で送信する HTTP ヘッダーを入力します。  **[本文]** は、**[Available keywords]\(使用可能なキーワード\)** ボックスの一覧を使ってメッセージをカスタマイズし、メッセージ送信時にアラート データを動的に挿入することができます。  **[ヘッダー]** と **[本文]** は、既定では、以下の例のような XML ペイロードが設定されます。
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > **[テスト通知]** ボタンをクリックすることで HTTP Post 通知をテストできますが、このテストはアラートの構成を保存した後にのみ実行できます。
    > 
    > 
13. **[保存]** ボタンをクリックして、アラートの構成を保存します。  手順 5 で **[有効な警告]** をオンにした場合は、ここでアラートがアクティブになります。

## <a name="next-steps"></a>次の手順
* [Azure CDN のリアルタイム統計情報](cdn-real-time-stats.md)
* [詳細な HTTP レポート](cdn-advanced-http-reports.md)
* [使用量パターン](cdn-analyze-usage-patterns.md)

