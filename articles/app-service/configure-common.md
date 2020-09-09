---
title: ポータルでアプリを構成する
description: Azure portal で App Service アプリの一般的な設定を構成する方法について説明します。 アプリの設定、接続文字列、プラットフォーム、言語スタック、コンテナーなど
keywords: Azure App Service、Web アプリ、アプリ設定、環境変数
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 4990862bacbf2afe2d4b5f39c2b9d31a7c48b78e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962895"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure portal で App Service アプリを構成する

このトピックでは、[Azure portal] を使用して Web アプリ、モバイル バックエンド、または API アプリの一般的な設定を構成する方法について説明します。

## <a name="configure-app-settings"></a>アプリケーションの設定の構成

App Service では、アプリ設定は、環境変数としてアプリケーション コードに渡される変数です。 Linux アプリとカスタム コンテナーの場合、App Service では、コンテナー内に環境変数を設定するためのアプリ設定が `--env` フラグを使用してコンテナーに渡されます。

[Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 

![App Services を探す](./media/configure-common/search-for-app-services.png)

アプリの左側のメニューで、 **[構成]**  >  **[アプリケーションの設定]** を選択します。

![アプリケーションの設定](./media/configure-common/open-ui.png)

ASP.NET および ASP.NET Core 開発者の場合、App Service でのアプリ設定の設定は *Web.config* または *appsettings.json* での `<appSettings>` の設定と同様ですが、App Service の値によって *Web.config* または *appsettings.json* でそれらがオーバーライドされます。 運用シークレット (Azure MySQL データベースのパスワードなど) を除き、*Web.config* または *appsettings.json* 内の開発設定 (ローカルの MySQL パスワードなど) を App Service で安全に保持できます。 ローカルでデバッグするときに開発設定を使用するコードと、Azure にデプロイされたときに運用シークレットを使用するコードは同じです。

同様に、他の言語スタックも実行時に環境変数としてアプリ設定を取得します。 言語スタック固有の手順については、次を参照してください。

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [カスタム コンテナー](configure-custom-container.md#configure-environment-variables)

アプリの設定は、格納されるときに常に暗号化されます (保存時の暗号化)。

> [!NOTE]
> アプリ設定は、[Key Vault 参照](app-service-key-vault-references.md)を使用して [Key Vault](../key-vault/index.yml) から解決することもできます。

### <a name="show-hidden-values"></a>非表示の値を表示する

既定では、アプリ設定の値は、セキュリティのためにポータルでは非表示になっています。 アプリ設定の非表示の値を表示するには、その設定の **[値]** フィールドをクリックします。 すべてのアプリ設定の値を表示するには、 **[値の表示]** ボタンをクリックします。

### <a name="add-or-edit"></a>追加または編集する

新しいアプリ設定を追加するには、 **[新しいアプリケーション設定]** をクリックします。 ダイアログで、[設定を現在のスロットに固有として構成する](deploy-staging-slots.md#which-settings-are-swapped)ことができます。

設定を編集するには、右側にある **[編集]** ボタンをクリックします。

完了したら、 **[更新]** をクリックします。 忘れずに **[構成]** ページに戻って **[保存]** をクリックください。

> [!NOTE]
> 既定の Linux コンテナーまたはカスタム Linux コンテナーでは、`ApplicationInsights:InstrumentationKey` などの、アプリ設定名の中の入れ子になった JSON キー構造はすべて、App Service ではキー名を `ApplicationInsights__InstrumentationKey` として構成する必要があります。 つまり、`:` はすべて `__` (二重アンダースコア) で置き換える必要があります。
>

### <a name="edit-in-bulk"></a>一括で編集する

アプリ設定を一括で追加または編集するには、 **[Advanced edit] (詳細な編集)** ボタンをクリックします。 完了したら、 **[更新]** をクリックします。 忘れずに **[構成]** ページに戻って **[保存]** をクリックください。

アプリ設定には、次の JSON 形式が含まれています。

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>接続文字列の構成

[Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの左側のメニューで、 **[構成]**  >  **[アプリケーションの設定]** を選択します。

![アプリケーションの設定](./media/configure-common/open-ui.png)

ASP.NET および ASP.NET Core 開発者の場合、App Service での接続文字列の設定は *Web.config* 内の `<connectionStrings>` での設定と同様ですが、App Service で設定した値によって *Web.config* 内の値がオーバーライドされます。*Web.config* 内の開発設定 (データベース ファイルなど) や運用シークレット (SQL Database の資格情報など) を App Service で安全に保持できます。 ローカルでデバッグするときに開発設定を使用するコードと、Azure にデプロイされたときに運用シークレットを使用するコードは同じです。

他の言語スタックの場合は、値にアクセスするために接続文字列の変数キーに特殊な形式が必要になるため、代わりに[アプリ設定](#configure-app-settings)を使用することをお勧めします。 ただし、例外が 1 つあります。特定の Azure データベースの種類は、その接続文字列をアプリで構成した場合、アプリと共にバックアップされます。 詳細については、「[バックアップ対象](manage-backup.md#what-gets-backed-up)」を参照してください。 この自動バックアップが必要ない場合は、アプリ設定を使用してください。

実行時に、接続文字列は、前に次の接続の種類が付加された環境変数として使用できます。

* SQLServer: `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* カスタム: `CUSTOMCONNSTR_`
* PostgreSQL: `POSTGRESQLCONNSTR_`  

たとえば、*connectionstring1* という名前の MySql 接続文字列には環境変数 `MYSQLCONNSTR_connectionString1` としてアクセスできます。 言語スタック固有の手順については、次を参照してください。

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [カスタム コンテナー](configure-custom-container.md#configure-environment-variables)

接続文字列は、格納されるときに常に暗号化されます (保存時の暗号化)。

> [!NOTE]
> 接続文字列は、[Key Vault 参照](app-service-key-vault-references.md)を使用して [Key Vault](../key-vault/index.yml) から解決することもできます。

### <a name="show-hidden-values"></a>非表示の値を表示する

既定では、接続文字列の値は、セキュリティのためにポータルでは非表示になっています。 接続文字列の非表示の値を表示するには、その文字列の **[値]** フィールドをクリックするだけです。 すべての接続文字列の値を表示するには、 **[値の表示]** ボタンをクリックします。

### <a name="add-or-edit"></a>追加または編集する

新しい接続文字列を追加するには、 **[新しい接続文字列]** をクリックします。 ダイアログで、[接続文字列を現在のスロットに固有として構成する](deploy-staging-slots.md#which-settings-are-swapped)ことができます。

設定を編集するには、右側にある **[編集]** ボタンをクリックします。

完了したら、 **[更新]** をクリックします。 忘れずに **[構成]** ページに戻って **[保存]** をクリックください。

### <a name="edit-in-bulk"></a>一括で編集する

接続文字列を一括で追加または編集するには、 **[Advanced edit] (詳細な編集)** ボタンをクリックします。 完了したら、 **[更新]** をクリックします。 忘れずに **[構成]** ページに戻って **[保存]** をクリックください。

接続文字列には、次の JSON 形式が含まれています。

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>全般設定を構成する

[Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの左側のメニューで、 **[構成]**  >  **[全般設定]** を選択します。

![全般設定](./media/configure-common/open-general.png)

ここでは、アプリのいくつかの一般的な設定を構成できます。 一部の設定では、[より高い価格レベルにスケールアップする](manage-scale-up.md)必要があります。

- **[Stack settings] (スタックの設定)** : アプリを実行するためのソフトウェア スタック (言語や SDK バージョンを含む)。 Linux アプリやカスタム コンテナー アプリの場合は、オプションのスタートアップ コマンドまたはファイルを設定することもできます。
- **[プラットフォームの設定]** : ホスティング プラットフォームの設定を構成できます。次のものが含まれます。
    - **[ビット]** : 32 ビットまたは 64 ビット。
    - **[WebSocket プロトコル]** : [ASP.NET SignalR] や [socket.io](https://socket.io/) など。
    - **[常時接続]** : トラフィックがない場合も、アプリを読み込まれたままにします。 継続的な Web ジョブや、CRON 式を使用してトリガーされる Web ジョブに対して有効にする必要があります。
      > [!NOTE]
      > [常時接続] 機能を使用すると、フロントエンド ロード バランサーはアプリケーション ルートに要求を送信します。 この App Service のアプリケーション エンドポイントを構成することはできません。
    - **[Managed pipeline version] (マネージド パイプライン バージョン)** : IIS の[パイプライン モード]。 以前のバージョンの IIS を必要とするレガシ アプリがある場合は、これを **[Classic] (クラシック)** に設定します。
    - **[HTTP version] (HTTP バージョン)** :[HTTPS/2](https://wikipedia.org/wiki/HTTP/2) プロトコルのサポートを有効にするには、 **[2.0]** に設定します。
    > [!NOTE]
    > 最新のブラウザーのほとんどは、TLS 上でのみ HTTP/2 プロトコルをサポートし、暗号化されていないトラフィックには引き続き HTTP/1.1 を使用しています。 クライアント ブラウザーが HTTP/2 でご利用のアプリに確実に接続されるようにするには、カスタム DNS 名をセキュリティで保護します。 詳細については、「[Azure App Service で TLS/SSL バインディングを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。
    - **[ARR affinity] (ARR アフィニティ)** :マルチインスタンス デプロイでは、クライアントがセッションの有効期間を通して同じインスタンスにルーティングされることを確認してください。 ステートレス アプリケーションの場合は、このオプションを **[オフ]** に設定できます。
- **[デバッグ]** : [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)、[ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)、または [Node.js](configure-language-nodejs.md#debug-remotely) アプリに対するリモート デバッグを有効にします。 このオプションは、48 時間後に自動的に無効になります。
- **[Incoming client certificates] (受信クライアント証明書)** : [相互認証](app-service-web-configure-tls-mutual-auth.md)でクライアント証明書を必要とします。

## <a name="configure-default-documents"></a>既定のドキュメントを構成する

この設定は、Windows アプリでのみ使用されます。

[Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの左側のメニューで、 **[構成]**  >  **[既定のドキュメント]** を選択します。

![既定のドキュメント](./media/configure-common/open-documents.png)

既定のドキュメントは、Web サイトのルート URL に表示される Web ページです。 一覧で最初に一致するファイルが使用されます。 新しい既定のドキュメントを追加するには、 **[New document] (新しいドキュメント)** をクリックします。 忘れずに **[保存]** をクリックください。

アプリが、静的コンテンツの処理ではなく URL に基づいてルーティングされるモジュールを使用している場合、既定のドキュメントは必要ありません。

## <a name="configure-path-mappings"></a>パス マッピングを構成する

[Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの左側のメニューで、 **[構成]**  >  **[Path mappings]\(パスのマッピング\)** を選択します。

![パスのマッピング](./media/configure-common/open-path.png)

**[Path mappings] (パス マッピング)** ページには、OS の種類に基づいてさまざまな内容が表示されます。

### <a name="windows-apps-uncontainerized"></a>Windows アプリ (コンテナー化されていない)

Windows アプリの場合は、IIS ハンドラー マッピングや仮想アプリケーションおよびディレクトリをカスタマイズできます。

ハンドラー マッピングを使用すると、特定のファイル拡張子への要求を処理するためのカスタム スクリプト プロセッサを追加できます。 カスタム ハンドラーを追加するには、 **[New handler] (新しいハンドラー)** をクリックします。 次のようにハンドラーを構成します。

- **[拡張子]** 。 処理するファイル拡張子 ( *\*.php* や *handler.fcgi* など)。
- **[Script processor] (スクリプト プロセッサ)** 。 スクリプト プロセッサの絶対パス。 ファイル拡張子に一致するファイルへの要求は、スクリプト プロセッサによって処理されます。 アプリのルート ディレクトリは `D:\home\site\wwwroot` というパスを使って参照します。
- **[引数]** 。 スクリプト プロセッサの省略可能なコマンド ライン引数。

各アプリには、`D:\home\site\wwwroot` にマップされた既定のルート パス (`/`) があります。コードは、既定ではここにデプロイされます。 アプリのルートが別のフォルダーにあるか、またはリポジトリに複数のアプリケーションが含まれている場合は、ここで仮想アプリケーションおよびディレクトリを編集または追加できます。 **[New virtual application or directory] (新しい仮想アプリケーションまたはディレクトリ)** をクリックします。

仮想アプリケーションおよびディレクトリを構成するには、各仮想ディレクトリと、Web サイトのルート (`D:\home`) を基準としたその対応する物理パスを指定します。 必要に応じて、 **[アプリケーション]** チェック ボックスをオンにして、仮想ディレクトリをアプリケーションとしてマークすることもできます。

### <a name="containerized-apps"></a>コンテナー化されたアプリ

[コンテナー化されたアプリのカスタム ストレージを追加](configure-connect-to-azure-storage.md)できます。 コンテナー化されたアプリには、すべての Linux アプリのほか、App Service 上で実行されている Windows および Linux カスタム コンテナーも含まれます。 **[New Azure Storage Mount] (新しい Azure Storage のマウント)** をクリックし、次のようにカスタム ストレージを構成します。

- **Name**:表示名です。
- **[構成オプション]** : **[基本]** または **[詳細]** 。
- **[ストレージ アカウント]** :必要なコンテナーを含むストレージ アカウント。
- **[Storage type]\(ストレージの種類\)** : **[Azure BLOB]** または **[Azure Files]** 。
  > [!NOTE]
  > Windows コンテナー アプリは Azure Files のみをサポートします。
- **ストレージ コンテナー**:基本構成のときの必要なコンテナー。
- **[Share name] (共有名)** : 詳細な構成のときのファイル共有名。
- **[アクセス キー]** : 詳細な構成のときのアクセス キー。
- **[Mount path] (マウント パス)** : カスタム ストレージをマウントするためのコンテナー内の絶対パス。

詳細については、「[App Service 内のコンテナーからネットワーク共有としての Azure Storage にアクセスする](configure-connect-to-azure-storage.md)」を参照してください。

## <a name="configure-language-stack-settings"></a>言語スタックの設定を構成する

Linux アプリについては、次を参照してください。

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>カスタム コンテナーを構成する

「[Azure App Service のカスタム Linux コンテナーを構成する](configure-custom-container.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure App Service のカスタム ドメイン名の構成]
- [Azure App Service でステージング環境を設定する]
- [Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)
- [診断ログを有効にする](troubleshoot-diagnostic-logs.md)
- [Azure App Service でのアプリのスケーリング]
- [Azure App Service での監視の基本]
- [applicationHost.xdt を使用して applicationHost.config 設定を変更する](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service のカスタム ドメイン名の構成]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service でステージング環境を設定する]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service での監視の基本]: ./web-sites-monitor.md
[パイプライン モード]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service でのアプリのスケーリング]: ./manage-scale-up.md