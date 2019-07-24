---
title: Mobile Services から App Service モバイル アプリへの移行
description: Mobile Services アプリケーションを App Service モバイル アプリに簡単に移行する方法について説明します。
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: dfc5e2923215b1669b0a3300653ad0cae7379655
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960737"
---
# <a name="article-top"></a>既存の Azure Mobile Service を Azure App Service に移行する
[Azure App Service は一般公開されており]、Azure Mobile Service サイトを簡単にインプレース移行し、Azure App Service の全機能を最大限に活用できます。  このドキュメントでは、Azure Mobile Service から Azure App Service にサイトを移行するときに必要な作業について説明します。

## <a name="what-does-migration-do"></a>移行がサイトにもたらすもの
Azure Mobile Service を移行すれば、コードを変更せずにモバイル サービスを [Azure App Service] アプリに変えることができます。  Notification Hubs、SQL データ接続、認証設定、スケジュールされたジョブ、ドメイン名は変更されません。  Azure Mobile Service を利用しているモバイル クライアントは通常どおりの動作を続けます。  サービスは Azure App Service に移行されると再起動します。

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>サイトを移行する理由
Microsoft では、次のような Azure App Service の機能を最大限に活用できるように Azure Mobile Service の移行を推奨しています。

* [WebJobs] や [custom domain names] などの新しいホスト機能。
* [Application Insights] による監視とトラブルシューティング。
* [ステージング スロット]、ロールバック、本稼動テストなど、組み込み DevOps ツール。
* [自動スケール]、負荷分散、[パフォーマンス監視]。

Azure App Service の利点の詳細については、[Mobile Services と App Service の比較]に関するトピックを参照してください。

## <a name="before-you-begin"></a>開始する前に
サイトに対する大規模な作業に着手する前に、SQL データベースとモバイル サービス スクリプトをバックアップする必要があります。

## <a name="migrating-site"></a>サイトを移行する
この移行プロセスでは、単一の Azure リージョン内のすべてのサイトを移行します。

サイトを移行するには:

1. [Azure クラシック ポータル]にログインします。
2. 移行するリージョン内のモバイル サービスを選択します。
3. **[App Service に移行する]** ボタンをクリックします。

   ![[移行] ボタン][0]
4. [App Service に移行する] ダイアログを読みます。
5. ボックスにモバイル サービスの名前を入力します。  たとえば、ドメイン名が "contoso.azure-mobile.net" の場合、ボックスに「*contoso*」と入力します。
6. チェック マーク ボタンをクリックします。

アクティビティ モニターでの移行の状態を監視します。 Azure クラシック ポータルで、サイトが一覧に "*移行中*" として表示されます。

  ![移行アクティビティ モニター][1]

移行するモバイル サービスごとに移行には 3 分から 15 分かかります。  移行中も、サイトは引き続き利用できます。
サイトは移行プロセスの最後に再起動されます。  再起動プロセスの間、サイトは数秒間利用できなくなります。

## <a name="finalizing-migration"></a>移行の最終処理
移行プロセスの終わりに、モバイル クライアントからサイトをテストします。  モバイル クライアントを変更せず、あらゆる共通クライアント アクションを実行できることを確認します。  

### <a name="update-app-service-tier"></a>適切な App Service 価格レベルを選択する
Azure App Service に移行した後は、価格設定がより自由になります。

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [設定] メニューの **[App Service プラン]** をクリックします。
5. **[価格レベル]** タイルをクリックします。
6. 要件に合うタイルをクリックし、**[選択]** をクリックします。  利用できる価格レベルを表示するには、**[すべて表示]** をクリックする必要があります。

最初は以下のレベルを推奨します。

| モバイル サービス価格レベル | App Service 価格レベル |
|:--- |:--- |
| 無料 |F1 Free |
| Basic |B1 Basic |
| Standard |S1 Standard |

自分のアプリケーションに適した価格レベルを自由に選択できます。  App Service の価格に関する詳細については、 「 [App Service 価格] 」を参照してください。

> [!TIP]
> App Service Standard レベルでは、[ステージング スロット]、自動バックアップ、自動スケールなど、さまざまな機能にアクセスできます。  この機会に新しい機能をお試しください。
>
>

### <a name="review-migration-scheduler-jobs"></a>移行された Scheduler Jobs の確認
Scheduler Jobs は移行後約 30 分経過するまで表示されません。  スケジュールされたジョブは、引き続きバックグラウンドで実行されます。
再度表示できるようになった後で、スケジュールされたジョブを表示するには、次の手順に従います。

1. [Azure Portal] にログインします。
2. **[参照]** を選択し、*[フィルター]* ボックスに「**Schedule**」と入力し、**[Scheduler コレクション]** を選択します。

移行後に利用できる無料スケジューラ ジョブの数は限られています。  使用状況と [Azure Scheduler プラン]を確認してください。

### <a name="configure-cors"></a>必要に応じて CORS を構成する
クロス オリジン リソース共有は、Web サイトが異なるドメインの Web API にアクセスする手法です。  使用している Azure Mobile Services に Web サイトが関連付けられている場合、移行の一環として CORS を構成する必要があります。  Azure Mobile Services へのアクセスをモバイル デバイスに限定していた場合、特別な場合を除き、CORS を構成する必要はありません。

移行した CORS 設定は **MS_CrossDomainWhitelist** アプリ設定として利用できます。  App Service の CORS 機能をサイトに移行するには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. API メニューの **[CORS]** をクリックします。
5. 許可されたオリジンをボックスに 1 つずつ入力し、Enter を押します。
6. 許可されたオリジンの一覧が正しければ、[保存] ボタンをクリックします。

> [!TIP]
> Azure App Service を使用する利点の 1 つは、同じサイトで Web サイトとモバイル サービスを実行できることです。  詳細については、「[次のステップ](#next-steps)」を参照してください。
>
>

### <a name="download-publish-profile"></a>新しい発行プロファイルのダウンロード
Azure App Service に移行すると、サイトの発行プロファイルが変更されます。  Visual Studio 内からサイトを発行する場合、新しい発行プロファイルが必要になります。  新しい発行プロファイルをダウンロードするには、次の手順に従います。

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. **[発行プロファイルの取得]** をクリックします。

PublishSettings ファイルがコンピューターにダウンロードされます。  通常、ファイル名は <*サイト名*>.PublishSettings です。  以下の手順で、発行設定を既存のプロジェクトにインポートします。

1. Visual Studio を開き、Azure Mobile Service プロジェクトを開きます。
2. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** を選択します。
3. **[インポート]** をクリックします。
4. **[参照]** をクリックし、ダウンロードした発行設定ファイルを選択します。  **[OK]**
5. **[接続の検証]** をクリックして、発行設定が機能していることを確認します。
6. **[発行]** をクリックして、サイトを発行します。

## <a name="working-with-your-site"></a>サイトの移行後の操作
移行後に [Azure Portal] で新しい App Service を操作します。  以下は、 [Azure クラシック ポータル]で実行していた特定の操作とそれに対応する App Service 操作に関する注記です。

### <a name="publishing-your-site"></a>移行したサイトをダウンロードし、公開する
サイトは git または ftp 経由で利用可能であり、WebDeploy、TFS、Mercurial、GitHub、FTP など、さまざまなメカニズムで再公開できます。  デプロイメント資格情報はサイトの残りの部分で移行されます。  デプロイメント資格情報を設定しなかった場合、または資格情報を覚えていない場合はリセットできます。

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [公開] メニューの **[デプロイメント資格情報]** をクリックします。
5. ボックスに新しいデプロイメント資格情報を入力し、[保存] ボタンをクリックします。

これらの資格情報を利用し、git でサイトの複製を作成したり、GitHub、TFS、Mercurial から自動化デプロイを設定したりできます。  詳細については、[Azure App Service のデプロイに関するドキュメント] を参照してください。

### <a name="appsettings"></a>アプリケーションの設定
移行したモバイル サービスのほとんどの設定は [アプリケーション設定] から利用できます。  アプリ設定の一覧は [Azure Portal] から取得できます。
アプリケーションの設定を表示または変更するには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [全般] メニューの **[アプリケーション設定]** をクリックします。
5. [アプリケーション設定] セクションまでスクロールし、アプリ設定を見つけます。
6. アプリ設定の値をクリックし、値を編集します。  **[保存]** をクリックして値を保存します。

複数のアプリ設定を同時に更新できます。

> [!TIP]
> 同じ値を含むアプリケーション設定が 2 つあります。  たとえば、*ApplicationKey* と *MS\_ApplicationKey* の 2 つがあります。  両方のアプリケーション設定を同時に更新します。
>
>

### <a name="authentication"></a>認証
すべての認証設定は、移行したサイトでアプリケーション設定として利用できます。  認証設定を更新するには、適切なアプリケーション設定を変更する必要があります。  次の表は、ご利用の認証プロバイダーに適したアプリケーションの設定をまとめたものです。

| プロバイダー | クライアント ID | クライアント シークレット | その他の設定 |
|:--- |:--- |:--- |:--- |
| Microsoft アカウント |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

注:**MS\_AadTenants** はテナント ドメインのコンマ区切りリストとして格納されています (Mobile Services ポータルの [許可されているテナント] フィールド)。

> [!WARNING]
> **[設定] メニューの認証メカニズムは使用しないでください。**
>
> Azure App Service の *[認証/承認]* 設定メニューに別個の "コードなし" 認証/承認システムがあり、[設定] メニューに (非推奨の) *[モバイル認証]* オプションがあります。  これらのオプションと移行した Azure モバイル サービスとの間には互換性がありません。  [サイトをアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md) し、Azure App Service 認証を最大限に活用できます。
>
>

### <a name="easytables"></a>データ
Mobile Services の *[データ]* タブは Azure Portal では *[テーブルの簡単操作]* に取って代わられました。  テーブルの簡単操作にアクセスするには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [モバイル] メニューの **[テーブルの簡単操作]** をクリックします。

**[追加]** ボタンをクリックして新しいテーブルを追加したり、テーブル名をクリックして既存のテーブルにアクセスしたりできます。  このブレードからは次のようなさまざまな操作を実行できます。

* テーブルのアクセス許可を変更する
* 操作スクリプトを編集する
* テーブル スキーマを管理する
* テーブルを削除する
* テーブルの内容を消去する
* テーブルの特定の行を削除する

### <a name="easyapis"></a>API
Mobile Services の *[API]* タブは Azure Portal では *[API の簡単操作]* に取って代わられました。  API の簡単操作にアクセスするには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [モバイル] メニューの **[API の簡単操作]** をクリックします。

移行した API は既にブレードに一覧表示されています。  このブレードから API を追加することもできます。  特定の API を管理するには、その API をクリックします。
新しいブレードから、アクセス許可を調整したり、API のスクリプトを編集したりできます。

### <a name="on-demand-jobs"></a>スケジューラ ジョブ
スケジューラ ジョブはすべて、[スケジューラ ジョブ コレクション] セクションから利用できます。  スケジューラ ジョブにアクセスするには、次の手順に従います。

1. [Azure Portal] にログインします。
2. **[参照]** を選択し、*[フィルター]* ボックスに「**Schedule**」と入力し、**[Scheduler コレクション]** を選択します。
3. サイトのジョブ コレクションを選択します。  <*サイト名*>-Jobs という名前が付けられます。
4. **[設定]** をクリックします。
5. [管理] の **[スケジューラ ジョブ]** をクリックします。

スケジュールされたジョブは、移行前に指定した頻度で表示されます。  オンデマンド ジョブは無効になります。  オンデマンド ジョブを実行するには、次の手順に従います。

1. 実行するジョブを選択します。
2. 必要に応じて、**[有効にする]** をクリックしてジョブを有効にします。
3. **[設定]**、**[スケジュール]** の順にクリックします。
4. 定期的なアイテムとして **[1 回]** を選択し、**[保存]** をクリックします。

オンデマンド ジョブは `App_Data/config/scripts/scheduler post-migration`にあります。  すべてのオンデマンド ジョブを [WebJobs] または[関数]に変更することが推奨されます。  新しいスケジューラ ジョブは、[WebJobs] または[関数]として記述してください。

### <a name="notification-hubs"></a>Notification Hubs
Mobile Services では、プッシュ通信に Notification Hubs が使用されます。  次のアプリケーション設定を使用して、移行後、モバイル サービスに通知ハブをリンクします。

| アプリケーション設定 | 説明 |
|:--- |:--- |
| **MS\_PushEntityNamespace** |通知ハブの名前空間 |
| **MS\_NotificationHubName** |通知ハブの名前 |
| **MS\_NotificationHubConnectionString** |通知ハブの接続文字列 |
| **MS\_NamespaceName** |MS_PushEntityNamespace のエイリアス |

通知ハブは [Azure Portal] 経由で管理されます。  Notification Hub 名を書き留めます (アプリケーション設定で見つかります)。

1. [Azure Portal] にログインします。
2. **[参照]** **[通知ハブ]** の順に選択します。
3. モバイル サービスに関連付けられている通知ハブの名前をクリックします。

> [!NOTE]
> 通知ハブが "Mixed" 型の場合は表示されません。  「Mixed」タイプの通知ハブでは、Notification Hubs と以前の Service Bus 機能の両方が利用されます。  [Mixed 名前空間を変換]してから、次に進みます。  変換が完了すると、通知ハブが [Azure Portal] に表示されます。
>
>

詳細については、 [Notification Hubs] ドキュメントを確認してください。

> [!TIP]
> [Azure Portal] の Notification Hubs 管理機能はまだプレビュー段階です。  [Azure クラシック ポータル] で引き続きすべての Notification Hubs を管理できます。
>
>

### <a name="legacy-push"></a>従来のプッシュ設定
Notification Hubs での導入前にモバイル サービスでプッシュを構成した場合、使われているのは "*従来のプッシュ*" です。  プッシュを使用しており、構成に通知ハブが表示されていない場合は、"*従来のプッシュ*" が使われているものと思われます。  この機能は、他のすべての機能と共に移行されます。  ただし、移行の完了後すぐに Notification Hubs にアップグレードすることをお勧めします。

それまでの間、従来のプッシュ設定はすべて [アプリ設定] で利用できます (大きな例外は APNs 証明書)。  APNS 証明書を更新します。そのためには、ファイル システムで適切なファイルを置き換えます。

### <a name="app-settings"></a>その他のアプリ設定
次の追加アプリ設定はモバイル サービスから移行され、 *[設定]* > *App [設定]* にあります。

| アプリケーション設定 | 説明 |
|:--- |:--- |
| **MS\_MobileServiceName** |アプリの名前 |
| **MS\_MobileServiceDomainSuffix** |ドメインのプレフィックス。 つまり  azure-mobile.net |
| **MS\_ApplicationKey** |アプリケーション キー |
| **MS\_MasterKey** |アプリ マスター キー |

アプリケーション キーとマスター キーは、元のモバイル サービスのアプリケーション キーと同じになる必要があります。  具体的に言うと、アプリケーション キーはモバイル クライアントのモバイル API の利用を検証するために送信されます。

### <a name="cliequivalents"></a>対応するコマンド ライン
*azure モバイル* コマンドを利用して Azure Mobile Services サイトを管理することができなくなります。  多くの関数が *azure サイト* コマンドに取って代わられました。  下の表を利用し、一般的なコマンドに対応するものを探してください。

| *Azure Mobile* コマンド | 対応する *Azure サイト* コマンド |
|:--- |:--- |
| mobile locations |site location list |
| mobile list |site list |
| mobile show *name* |site show *name* |
| mobile restart *name* |site restart *name* |
| mobile redeploy *name* |site deployment redeploy *commitId* *name* |
| mobile key set *name* *type* *value* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile config list *name* |site appsetting list *name* |
| mobile config get *name* *key* |site appsetting show *key* *name* |
| mobile config set *name* *key* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile domain list *name* |site domain list *name* |
| mobile domain add *name* *domain* |site domain add *domain* *name* |
| mobile domain delete *name* |site domain delete *domain* *name* |
| mobile scale show *name* |site show *name* |
| mobile scale change *name* |site scale mode *mode* *name* <br /> site scale instances *instances* *name* |
| mobile appsetting list *name* |site appsetting list *name* |
| mobile appsetting add *name* *key* *value* |site appsetting add *key*=*value* *name* |
| mobile appsetting delete *name* *key* |site appsetting delete *key* *name* |
| mobile appsetting show *name* *key* |site appsetting delete *key* *name* |

適切なアプリケーション設定を更新し、認証またはプッシュ通信を更新します。
ファイルを編集し、ftp または git を使用してサイトを公開します。

### <a name="diagnostics"></a>診断とログ
通常、診断ログは Azure App Service で無効になっています。  診断ログを有効にするには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. [設定] ブレードが既定で開きます。
4. [機能] メニューで **[診断ログ]** を選択します。
5. ログ **[アプリケーション ログ (ファイル システム)]**、**[詳細なエラー メッセージ]**、**[失敗した要求トレース]** について [オン] をクリックします。
6. Web サーバーのログの **[ファイル システム]** をクリックします。
7. **[保存]**

ログを表示するには:

1. [Azure Portal] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
3. **[ツール]** ボタンをクリックします。
4. [監視] メニューの **[ログ ストリーム]** を選択します。

ログは、生成されるとウィンドウに表示されます。  ログをダウンロードし、後でデプロイ資格情報を利用して分析できます。 詳細については、[Logging]に関するドキュメントを参照してください。

## <a name="known-issues"></a>既知の問題
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>移行したモバイル アプリの複製を削除すると、サイトが停止する
Azure PowerShell を使用して、移行したモバイル サービスを複製し、その後複製を削除すると、運用サービスの DNS エントリが削除されます。  サイトは、インターネットからアクセスできなくなります。  

解決策:サイトを複製する必要がある場合は、ポータルを使用してください。

### <a name="changing-webconfig-does-not-work"></a>Web.config の変更が機能しない
ASP.NET サイトがある場合、`Web.config` ファイルの変更は適用されません。  Azure App Service は、Mobile Services ランタイムをサポートするために適切な `Web.config` ファイルを起動時に作成します。  XML 変換ファイルを使用することで特定の設定 (カスタム ヘッダーなど) をオーバーライドできます。  呼び出される `applicationHost.xdt` でファイルを作成します。このファイルは Azure Service の `D:\home\site` ディレクトリに格納されます。  カスタム デプロイ スクリプトで、または直接 Kudu を使用して、`applicationHost.xdt` ファイルをアップロードします。  次にドキュメントの例を示します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

詳細については、GitHub の「[XDT Transform Samples (XDT 変換サンプル)]」を参照してください。

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>移行された Mobile Services を Traffic Manager に追加できない
Traffic Manager プロファイルを作成するとき、移行されたモバイル サービスをプロファイルに直接選択することはできません。  "外部エンドポイント" を使用してください。  外部エンドポイントは、PowerShell でしか追加できません。  詳細については、[Traffic Manager のチュートリアル](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/)を参照してください。

## <a name="next-steps"></a>次のステップ
アプリケーションが App Service に移行され、活用できる機能がさらに増えました。

* デプロイメント [ステージング スロット] では、変更をサイトに公開し、A/B テストを実行できます。
* [WebJobs] は、オンデマンドでスケジュールしたジョブの代わりを提供します。
* GitHub、TFS、Mercurial にサイトをリンクし、サイトを [連続的にデプロイ] できます。
* [Application Insights] を利用し、サイトを監視できます。
* 同じコードから Web サイトと Mobile API にサービスを提供します。

### <a name="upgrading-your-site"></a>Azure Mobile Apps SDK に Mobile Services サイトをアップグレードする
* Node.js ベースのサーバー プロジェクトの場合、新しい [Mobile Apps Node.js SDK] はいくつかの新機能を提供します。 たとえば、ローカルで開発やデバッグを行い、0.10 より後のバージョンの Node.js を使用し、Express.js ミドルウェアでカスタマイズを行うことができます。
* .NET ベースのサーバー プロジェクトでは、新しい [Mobile Apps SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)は、NuGet の依存関係に関してより高い柔軟性を備えています。  これらのパッケージは、新しい App Service 認証をサポートし、任意の ASP.NET プロジェクトを作成できます。 アップグレードの詳細については、「 [既存の .NET Mobile Service の App Service へのアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md)」を参照してください。

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service 価格]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../azure-monitor/app/app-insights-overview.md
[自動スケール]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/overview.md
[Azure クラシック ポータル]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Azure Scheduler プラン]: ../scheduler/scheduler-plans-billing.md
[連続的にデプロイ]: ../app-service/deploy-continuous-deployment.md
[Mixed 名前空間を変換]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[custom domain names]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[Azure App Service は一般公開されており]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logging]: ../app-service/troubleshoot-diagnostic-logs.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services と App Service の比較]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[パフォーマンス監視]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[ステージング スロット]: ../app-service/deploy-staging-slots.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[XDT Transform Samples (XDT 変換サンプル)]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[関数]: ../azure-functions/functions-overview.md
