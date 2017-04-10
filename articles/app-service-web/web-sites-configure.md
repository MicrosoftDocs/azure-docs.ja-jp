---
title: "Azure App Service での Web アプリの構成"
description: "Azure App Service での Web アプリの構成方法"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 98e5764f84f23ec586ebd823634b9024e4689c5c
ms.lasthandoff: 01/20/2017


---
# <a name="configure-web-apps-in-azure-app-service"></a>Azure App Service での Web アプリの構成
このトピックでは、 [Azure Portal]を使用して Web アプリを構成する方法について説明します。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>アプリケーションの設定
1. [Azure Portal]で、Web アプリのブレードを開きます。
2. **[すべての設定]**をクリックします。
3. **[アプリケーションの設定]**をクリックします。

![アプリケーションの設定][configure01]

**[アプリケーション設定]** ブレードには、いくつかのカテゴリにグループ化された設定があります。

### <a name="general-settings"></a>全般設定
**[Framework バージョン]**。 アプリでこれらのフレームワークを使用する場合は、以下のオプションを設定します。 

* **.NET Framework**: .NET Framework のバージョンを設定します。 
* **PHP**: PHP のバージョンを設定するか、PHP を無効にする場合は **[オフ]** を設定します。 
* **Java**: Java のバージョンを選択するか、Java を無効にする場合は **[オフ]** を選択します。 **[Web コンテナー]** オプションを使用して Tomcat か Jetty のバージョンを選択します。
* **Python**: Python のバージョンを選択するか、Python を無効にする場合は **[オフ]** を選択します。

技術的な理由で、Web アプリで Java を有効にすると、.NET、PHP、Python オプションが無効になります。

<a name="platform"></a>
**プラットフォーム**。 Web アプリが 32 ビット環境で実行されるか 64 ビット環境で実行されるかを選択します。 64 ビット環境では Basic モードまたは Standard モードを使用する必要があります。 Free モードと Shared モードは常に 32 ビット環境で実行されます。

**[Web ソケット]**。 WebSocket プロトコルを有効にするには、**[オン]** を設定します (たとえば、Web アプリで [ASP.NET SignalR] または [socket.io] を使用する場合)。

<a name="alwayson"></a>
**常時接続**。 既定では、アイドル状態がしばらく続くと Web アプリはアンロードされます。 これにより、システムではリソースを節約できます。 基本モードと標準モードでは、**[常時接続]** を有効にすると、アプリが常に読み込まれた状態になります。 アプリで継続的な Web ジョブを実行する場合は、**[常時接続]** を有効にする必要があります。そうしないと、Web ジョブの実行の信頼性が低下する可能性があります。

**マネージ パイプライン バージョン**。 IIS [パイプライン モード]を設定します。 この設定は、以前のバージョンの IIS を必要とするレガシ アプリを使用する場合を除いて、[統合] \(既定) のままにしておきます。

**自動スワップ**。 デプロイ スロットの自動スワップを有効にした場合、App Service は、スロットに対して更新をプッシュしたときに、Web アプリを運用環境に自動的にスワップします。 詳細については、 [Azure App Service の Web アプリに対するステージング スロットへのデプロイ](web-sites-staged-publishing.md)に関するページをご覧ください。

### <a name="debugging"></a>デバッグ
**リモート デバッグ**。 リモート デバッグを有効にします。 これを有効にすると、Visual Studio でリモート デバッガーを使用して、Web アプリに直接接続できます。 リモート デバッグは 48 時間有効です。 

### <a name="app-settings"></a>アプリケーション設定
このセクションには、起動時に Web アプリがロードする名前/値ペアが含まれます。 

* .NET アプリの場合、実行時にこれらの設定が .NET 構成の `AppSettings` に挿入され、既存の設定がオーバーライドされます。 
* PHP、Python、Java および Node アプリケーションでは、実行時に環境変数としてこれらの設定にアクセスできます。 各アプリ設定で、2 つの環境変数が作成されます。1 つは、アプリ設定エントリで指定された名前になり、もう&1; つはプレフィックスとして APPSETTING_ が付加されます。 両方に同じ値が格納されます。

### <a name="connection-strings"></a>接続文字列
リンク済みリソースの接続文字列です。 

.NET アプリの場合、これらの接続文字列は、実行時に .NET 構成の `connectionStrings` 設定に挿入され、キーがリンクされたデータベース名である既存のエントリがオーバーライドされます。 

PHP、Python、Java、および Node アプリケーションの場合、実行時にこれらの設定が環境変数として使用できるようになり、環境変数にはプレフィックスとして接続の種類が付加されます。 環境変数使用されるプレフィックスは次のとおりです。 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* カスタム: `CUSTOMCONNSTR_`

たとえば、MySql の接続文字列が `connectionstring1` という名前であれば、`MYSQLCONNSTR_connectionString1` という環境変数でアクセスされます。

### <a name="default-documents"></a>既定のドキュメント
既定のドキュメントは、Web サイトのルート URL に表示される Web ページです。  一覧で最初に一致するファイルが使用されます。 

Web アプリでは、静的コンテンツを提供する代わりに URL に基づいてルーティングするモジュールを使用することがあります。その場合には、このような既定のドキュメントはありません。    

### <a name="handler-mappings"></a>ハンドラー マッピング
この領域では、特定のファイル拡張子に関する要求を処理するカスタム スクリプト プロセッサを追加します。 

* **[拡張子]**。 処理するファイル拡張子 (*.php や handler.fcgi など) を指定します。 
* **[スクリプト プロセッサ パス]**。 スクリプト プロセッサの絶対パスを指定します。 指定したファイル拡張子に一致するファイルに対する要求が、スクリプト プロセッサによって処理されます。 アプリのルート ディレクトリは `D:\home\site\wwwroot` というパスを使って参照します。
* **[追加の引数]**。 スクリプト プロセッサのオプションのコマンド ライン引数です。 

### <a name="virtual-applications-and-directories"></a>仮想アプリケーションとディレクトリ
仮想アプリケーションとディレクトリを構成するには、各仮想ディレクトリとそれに対応する物理パス (Web サイトのルートに対する相対パス) を指定します。 必要に応じて、 **[アプリケーション]** チェック ボックスをオンにして、仮想ディレクトリをアプリケーションとしてマークすることもできます。

## <a name="enabling-diagnostic-logs"></a>診断ログの有効化
診断ログを有効にするには

1. Web アプリのブレードで、 **[すべての設定]**をクリックします。
2. **[診断ログ]**をクリックします。 

ログ記録をサポートする Web アプリケーションの診断ログの書き込みに関するオプション: 

* **アプリケーションのログ記録**。 ファイル システムに、アプリケーションのログを書き込みます。 ログ記録は、12 時間継続されます。 

**レベル**。 アプリケーション ログが有効な場合、このオプションは記録する情報のレベルを指定します (エラー、警告、情報、または詳細)。

**Web サーバーのログ記録**。 ログは W3C 拡張ログ ファイル形式で保存されます。 

**詳細なエラー メッセージ**。 詳細なエラー メッセージの .htm ファイルを保存します。 ファイルは、/LogFiles/DetailedErrors に保存されます。 

**失敗した要求トレース**。 XML ファイルへのログ要求が失敗しました。 ファイルは /LogFiles/W3SVC*xxx*に保存されます (xxx は一意の識別子)。 このフォルダーには、1 つの XSL ファイルと&1; つ以上の XML ファイルが格納されます。 この XSL ファイルは、XML ファイルのコンテンツの書式設定とフィルター処理を行う役割を果たすため、必ずダウンロードしてください。

ログ ファイルを表示するには、次のように FTP の資格情報を作成する必要があります。

1. Web アプリのブレードで、 **[すべての設定]**をクリックします。
2. **[デプロイ資格情報]**をクリックします。
3. ユーザー名とパスワードを入力します。
4. **[保存]**をクリックします。

![デプロイ資格情報の設定][configure03]

完全な FTP ユーザー名は "app\username" です。*app* は Web アプリの名前です。 username は Web アプリ ブレードの **[Essentials]** の下に表示されます。  

![FTP デプロイ資格情報][configure02]

## <a name="other-configuration-tasks"></a>その他の 構成タスク
### <a name="ssl"></a>SSL
基本モードまたは標準モードでは、カスタム ドメインの SSL 証明書をアップロードすることができます。 詳細については、Web アプリに対する HTTPS の有効化に関するページをご覧ください。 

アップロードされた証明書を表示するには、 **[すべての設定]** > **[カスタム ドメインと SSL]**を使用して Web アプリを構成する方法について説明します。

### <a name="domain-names"></a>ドメイン名
Web アプリのカスタム ドメイン名を追加します。 詳細については、Azure App Service での Web アプリのカスタム ドメイン名の構成に関するページをご覧ください。

ドメイン名を表示するには、 **[すべての設定]** > **[カスタム ドメインと SSL]**を使用して Web アプリを構成する方法について説明します。

### <a name="deployments"></a>デプロイメント
* 継続的なデプロイを設定します。 [Git を使用した Azure App Service への Web Apps のデプロイ](web-sites-deploy.md)に関するページをご覧ください。
* デプロイメント スロット: 「[Azure App Service の Web アプリのステージング環境を設定する]」を参照してください。

デプロイ スロットを表示するには、 **[すべての設定]** > **[デプロイ スロット]**を使用して Web アプリを構成する方法について説明します。

### <a name="monitoring"></a>監視
基本モードまたは標準モードでは、最大&3; つの地理的に分散した場所から HTTP または HTTPS エンドポイントの可用性をテストすることができます。 HTTP 応答コードがエラー (4xx または 5xx) である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。 すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。 

詳細については、「 [方法: Web エンドポイントの状態を監視する]」をご覧ください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成]」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="next-steps"></a>次のステップ
* [Azure App Service のカスタム ドメイン名の構成]
* [アプリに対する HTTPS を Azure App Service で有効にする]
* [Azure App Service での Web アプリの拡張]
* [Azure App Service での Web Apps の監視の基本]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service のカスタム ドメイン名の構成]: ./web-sites-custom-domain-name.md
[Azure App Service の Web アプリのステージング環境を設定する]: ./web-sites-staged-publishing.md
[アプリに対する HTTPS を Azure App Service で有効にする]: ./web-sites-configure-ssl-certificate.md
[方法: Web エンドポイントの状態を監視する]: http://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service での Web Apps の監視の基本]: ./web-sites-monitor.md
[パイプライン モード]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service での Web アプリの拡張]: ./web-sites-scale.md
[socket.io]: ./web-sites-nodejs-chat-app-socketio.md
[Azure App Service アプリケーションの作成]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png

