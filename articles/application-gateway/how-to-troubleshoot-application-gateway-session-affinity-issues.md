---
title: セッション アフィニティに関する問題のトラブルシューティング
titleSuffix: Azure Application Gateway
description: この記事では、Azure Application Gateway のセッション アフィニティに関する問題のトラブルシューティング方法について説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 548bda36ed2b167c159d32a575b63ecbf10b16dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397571"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Azure Application Gateway のセッション アフィニティに関する問題をトラブルシューティングする

Azure Application Gateway のセッション アフィニティに関する問題を診断および解決する方法について説明します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

Cookie ベースのセッション アフィニティ機能は、同じサーバー上にユーザー セッションを保持する場合に便利です。 ゲートウェイで管理される Cookie を使用すると、Application Gateway は、ユーザー セッションの後続のトラフィックを、処理のために同じサーバーに送ることができます。 この機能は、ユーザー セッションのためにセッションの状態をサーバー上でローカルに保存する場合に重要です。

## <a name="possible-problem-causes"></a>考えられる問題の原因

Cookie ベースのセッション アフィニティの維持に関する問題が発生する主な原因には以下のものがあります。

- 「Cookie ベースのアフィニティ」設定が有効になっていない
- アプリケーションが Cookie ベースのアフィニティを処理できない
- アプリケーションは Cookie ベースのアフィニティを使用しているが、バックエンド サーバー間で要求がまだバウンスしている

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>「Cookie ベースのアフィニティ」設定が有効になっているかどうかを確認する

「Cookie ベースのアフィニティ」設定を有効にし忘れたときに、セッション アフィニティに関する問題が発生することがあります。 Azure portal の [HTTP 設定] タブで「Cookie ベースのアフィニティ」設定が有効になっているかどうかを確認するのには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にログオンします。

2. **左のナビゲーション** ウィンドウで、 **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードでアプリケーション ゲートウェイ名をクリックします。 選択したサブスクリプションに既存のリソースが複数ある場合は、 **[名前でフィルター]** ボックスにアプリケーション ゲートウェイ名を入力すると、 目的のアプリケーション ゲートウェイがすぐに見つかります。

3. **[設定]** の下で **[HTTP 設定]** タブを選択します。

   ![[設定] のスクリーンショット。[HTTP 設定] が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 右側にある **appGatewayBackendHttpSettings** をクリックして、Cookie ベースのアフィニティに対して **[有効]** を選択しているかどうかを調べます。

   ![スクリーンショットからは、アプリ ゲートウェイのゲートウェイ設定を確認できます。たとえば、Cookie ベースのアフィニティが選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



次の方法のいずれかを使用して、「**backendHttpSettingsCollection**」の下で「**CookieBasedAffinity**」の値が *[有効]* に設定されているかどうかも調べられます。

- PowerShell で [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) を実行する
- Azure Resource Manager テンプレートを使用して、JSON ファイルを調べる

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>アプリケーションが Cookie ベースのアフィニティを処理できない

#### <a name="cause"></a>原因

アプリケーション ゲートウェイは、Cookie を使用することによってのみ、セッション ベースのアフィニティを実行できます。

#### <a name="workaround"></a>回避策

アプリケーションが Cookie ベースのアフィニティを処理できない場合は、外部または内部の Azure Load Balancer やその他のサード パーティのソリューションを使用する必要があります。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>アプリケーションは Cookie ベースのアフィニティを使用しているが、バックエンド サーバー間で要求がまだバウンスしている

#### <a name="symptom"></a>症状

Cookie ベースのアフィニティ設定を有効にしており、Internet Explorer で短い名前の URL (たとえば `http://website`) を使用して Application Gateway にアクセスすると、要求がまだバックエンド サーバー間でバウンスしています。

この問題を特定するには、次の手順に従います。

1. Application Gateway の背後にあるアプリケーション (この例では Fiddle を使用しています) に接続している「クライアント」で、Web デバッガー トレースを行います。
    **ヒント**: Fiddler を使用する方法がわからない場合は、下部にある **[I want to collect network traffic and analyze it using web debugger]** (ネットワーク トラフィックを収集し、Web デバッガーを使用して分析する) オプションをオンにします。

2. セッション ログを調べて分析し、クライアントから提供された Cookie に ARRAffinity 詳細があるかどうかを判断します。 Cookie セット内に "**ARRAffinity=** *ARRAffinityValue*" などの ARRAffinity 詳細が見つからない場合、クライアントが、Application Gateway によって提供される ARRA Cookie で応答していないことを意味します。
    次に例を示します。

    ![セッション ログのスクリーンショット。エンティティが 1 つ強調表示されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Cookie 情報を含む、HTTP の要求ヘッダーのスクリーンショット。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

アプリケーションは、応答を取得するまで、要求ごとに Cookie を設定しようとし続けます。

#### <a name="cause"></a>原因

Internet Explorer や他のブラウザーが、短縮名 URL で Cookie を格納することも使用することもできないので、この問題が発生します。

#### <a name="resolution"></a>解像度

この問題を解決するには、FQDN を使用して Application Gateway にアクセスする必要があります。 たとえば、[http://website.com](https://website.com/) または [http://appgw.website.com](http://website.com/) を使用します。

## <a name="additional-logs-to-troubleshoot"></a>トラブルシューティングのためのその他のログ

その他のログを収集して分析することで、それらの Cookie ベースのセッション アフィニティに関連した問題をトラブルシューティングできます

### <a name="analyze-application-gateway-logs"></a>Application Gateway のログの解析

Application Gateway のログを収集するには、次の手順に従います。

Azure Portal を使用したログの有効化

1. [Azure portal](https://portal.azure.com/) で、リソースを探して **[診断ログ]** をクリックします。

   Application Gateway では、次の 3 つのログを使用できます。アクセス ログ、パフォーマンス ログ、ファイアウォール ログ

2. データの収集を開始するには、 **[診断を有効にする]** をクリックします。

   ![アプリケーション ゲートウェイのスクリーンショット。診断ログが選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **[診断設定]** ブレードには、診断ログの設定が表示されます。 この例では、Log Analytics を使用してログを保存します。 **[Log Analytics]** の下にある **[構成]** をクリックして、ワークスペースを設定します。 イベント ハブとストレージ アカウントを使用して診断ログを保存することもできます。

   ![[診断設定] ウィンドウのスクリーンショット。Log Analytics の [構成] が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 設定を確認し、 **[保存]** をクリックします。

   ![[診断設定] ウィンドウのスクリーンショット。[保存] が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Application Gateway のアクセス ログの表示と分析

1. Azure portal の Application Gateway のリソース ビューの **[監視]** セクションで **[診断ログ]** を選択します。

   ![[監視] のスクリーンショット。[診断ログ] が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 右側で、 **[ログのカテゴリ]** の下のドロップダウン リストから「**ApplicationGatewayAccessLog**」を選択します。  

   ![[ログのカテゴリ] のドロップダウン リストのスクリーンショット。ApplicationGatewayAccessLog が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Application Gateway アクセス ログ リストで、分析しエクスポートするログをクリックして、JSON ファイルをエクスポートします。

4. 手順 3 でにエクスポートした JSON ファイルを CSV ファイルに変換し、それらを Excel、Power BI などのデータ視覚化ツールで表示します。

5. 次のデータを確認します。

- **ClientIP** – これは、接続しているクライアントからのクライアント IP アドレスです。
- **ClientPort** – これは、接続しているクライアントからの、要求のソース ポートです。
- **RequestQuery** – これは、要求が受信される宛先サーバーを示します。
- **Server-Routed**:要求が送信されるバックエンド プールのインスタンス。
- **X-AzureApplicationGateway-LOG-ID**:要求に使用する関連付け ID。 この ID を使用すると、バックエンド サーバー上のトラフィックの問題をトラブルシューティングできます。 次に例を示します。X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**:Application Gateway がバックエンドから受信した HTTP 応答コード。

  ![プレーンテキストのサーバー ステータスのスクリーンショット。ほぼぼかしが入っていますが clientPort と SERVER-ROUTED を確認できます。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

2 つの項目が同じ ClientIP および ClientPort から送信されており、同じバックエンド サーバーに送信されていることが示されている場合、Application Gateway が正しく構成されていることを意味します。

2 つの項目が同じ ClientIP および ClientPort から送信されており、別々のバックエンド サーバーに送信されていることが示されている場合、要求がバックエンド サーバー間でバウンスしていることを示します。下部にある **[アプリケーションが Cookie ベースのアフィニティを使用しているが、バックエンド サーバー間で要求がまだバウンスしている]** を選択して、これをトラブルシューティングしてください。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Web デバッガーを使用して HTTP または HTTPS トラフィックをキャプチャおよび分析する

Fiddler などの Web デバッグ ツールは、インターネットとテスト用コンピューター間のネットワーク トラフィックをキャプチャすることで Web アプリケーションのデバッグに役立ちます。 これらのツールを使用すると、着信および送信データを、ブラウザーが受信/送信するときに検査できます。 この例では、Fiddler には、Web アプリケーションに関するクライアント側の問題、特に認証関連の問題のトラブルシューティングに役立つ HTTP リプレイ オプションがあります。

任意の Web デバッガーを使用してください。 このサンプルでは、Fiddler を使用して、HTTP または HTTPS トラフィックをキャプチャおよび分析します。次の手順に従ってください。

1. <https://www.telerik.com/download/fiddler> から Fiddler ツールをダウンロードします。

    > [!NOTE]
    > キャプチャ側のコンピューターに .NET 4 がインストールされている場合は、Fiddler4 を選択します。 それ以外の場合は、Fiddler2 を選択します。

2. セットアップ実行可能ファイルを右クリックし、管理者として実行しインストールします。

    ![Fiddler ツール セットアップ プログラムとコンテキスト メニューのスクリーンショット。[管理者として実行] が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler を開くと、Fiddler はトラフィックのキャプチャを自動的に開始します (左下にある [キャプチャ] に注目してください)。 F12 キーを押して、トラフィックのキャプチャを開始または停止します。

    ![Fiddler Web Debugger のスクリーンショット。[キャプチャ] インジケーターが強調表示されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. ほとんどの場合、関心があるのは復号化された HTTPS トラフィックであるので、 **[ツール]**  >  **[Fiddler オプション]** の順に選択し、 **[Decrypt HTTPS traffic]** (HTTPS トラフィックの復号化) チェックボックスをオンにして、HTTPS 復号化を有効にすることができます。

    ![Fiddler オプションのスクリーンショット。HTTPS と [Decrypt HTTPS traffic] (HTTPS トラフィックの復号化) が選択されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 次のスクリーンショットに従って、 **[X]** (アイコン) > **[すべて削除]** をクリックすることにより、問題を再現する前に、関連付けられていない以前のセッションを削除できます。 

    ![X アイコンが選択されている画面のスクリーンショット。[すべて削除] オプションが表示されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 問題を再現したら、 **[ファイル]**  >  **[保存]**  >  **[すべてのセッション]** の順に選択することによりレビュー用のファイルを保存します。 

    ![[ファイル]、[保存]、[すべてのセッション] オプションを選択した画面のスクリーンショット。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. セッション ログを確認および分析して、問題を特定します。

    次に例を示します。

- **例 A:** 要求がクライアントから送信され、Application Gateway のパブリック IP アドレスに到達したことを示すセッション ログを探し、このログをクリックして詳細を表示します。  右側の下部のボックスのデータは、Application Gateway がクライアントに返しているものです。 [RAW] タブを選択し、クライアントが "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*" を受信しているかどうかを判断します。 Cookie がない場合は、セッション アフィニティが設定されていないか、Application Gateway が Cookie をクライアントに適用していません。

   > [!NOTE]
   > この ARRAffinity 値は cookie-id であり、Application Gateway がクライアントに対して特定のバックエンド サーバーに送信されるように設定する値です。

   ![ログ エントリの詳細例のスクリーンショット。Set-Cookie 値が強調表示されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **例 B:** 前のセッション ログが続く次のセッション ログは、Application Gateway に応答するクライアントであり、ARRAAFFINITY を設定しました。 ARRAffinity cookie-id が一致した場合、パケットは、以前に使用されていたものと同じバックエンド サーバーに送信されます。 HTTP 通信の次の数行を調べて、クライアントの ARRAffinity Cookie が変更されているかどうかを確認します。

   ![ログ エントリの詳細例のスクリーンショット。Cookie が強調表示されています。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 同じ通信セッションの場合、Cookie は変更しないでください。 右側にある上部のチェック ボックスをオンにして、[Cookies] タブを選択して、クライアントが Cookie を使用し Application Gateway に送信しているかどうかを確認します。 そうでない場合、クライアントのブラウザーは会話で Cookie を保持しておらず使用していません。 クライアントは嘘をつくことがあります。

 

## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。