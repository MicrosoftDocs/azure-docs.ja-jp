---
title: Azure Functions のデプロイ テクノロジ
description: Azure Functions にコードをデプロイするさまざまな方法について学習します。
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943637"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions のデプロイ テクノロジ

各種のテクノロジを使用して、Azure Functions プロジェクト コードを Azure にデプロイすることができます。 この記事では、使用可能なデプロイ方法の概要と、さまざまなシナリオで推奨される最適な方法について説明します。 また、基になるデプロイ テクノロジについての完全な一覧とその主要な詳細情報も提供します。 

## <a name="deployment-methods"></a>デプロイ方法

コードを Azure に発行するために使用するデプロイ テクノロジは、通常、アプリの公開方法によって決まります。 適切なデプロイ方法は、具体的なニーズと開発サイクルにおけるポイントによって決まります。 たとえば、開発およびテスト中であれば、Visual Studio Code などの開発ツールから直接配置します。 アプリが運用環境にある場合は、ソース管理から継続的に発行するか、または追加の検証とテストを含む自動化された発行パイプラインを使用する可能性が高くなります。  

次の表では、関数プロジェクトで使用できるデプロイ方法について説明します。

| デプロイ&nbsp;の種類 | メソッド | 最適なシナリオ |
| -- | -- | -- |
| ツールベース | &bull;&nbsp;[Visual&nbsp;Studio&nbsp;Code&nbsp;による発行](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio による発行](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Core Tools による発行](functions-run-local.md#publish) | 開発中のデプロイ、およびその他のアドホック デプロイ。 デプロイはツールによってローカルで管理されます。 | 
| App Service マネージド| &bull;&nbsp;[Deployment&nbsp;Center&nbsp;(CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[コンテナーの&nbsp;デプロイ](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  ソース管理またはコンテナー レジストリからの継続的配置 (CI/CD)。 デプロイは、App Service プラットフォーム (Kudu) によって管理されます。|
| 外部パイプライン|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub Actions](functions-how-to-github-actions.md) | 追加の検証、テスト、およびその他のアクションを含む運用および DevOps パイプラインは、自動デプロイの一部として実行されます。 デプロイはパイプラインによって管理されます。 |

特定の関数のデプロイでは、そのコンテキストに基づいて最適なテクノロジが使用されますが、ほとんどのデプロイ方法は [zip デプロイ](#zip-deploy)に基づいています。

## <a name="deployment-technology-availability"></a>デプロイ テクノロジの利用可否

Azure Functions は、クロス プラットフォームのローカル開発と、Windows と Linux でのホスティングをサポートしています。 現時点では、次の 3 つのホスティング プランを利用できます。

+ [従量課金プラン](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [専用 (App Service) プラン](dedicated-plan.md)

各プランの動作は異なります。 各種の Azure Functions に対してすべてのデプロイ テクノロジが使用できるわけではありません。 次の表は、オペレーティング システムとホスティング プランの各組み合わせでサポートされるデプロイ テクノロジを示しています。

| デプロイ テクノロジ | Windows Consumption | Windows Premium | Windows Dedicated  | Linux Consumption | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 外部パッケージ URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| ZIP デプロイ |✔|✔|✔|✔|✔|✔|
| Docker コンテナー | | | | |✔|✔|
| Web デプロイ |✔|✔|✔| | | |
| ソース管理 |✔|✔|✔| |✔|✔|
| ローカル Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| クラウドの同期<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| ポータルでの編集 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup>[トリガーの手動同期](#trigger-syncing)が必要なデプロイ テクノロジ。
<sup>2</sup> ポータルでの編集は、Premium プランと専用プランを使用する Linux 上の Functions の HTTP トリガーとタイマー トリガーに対してのみ使用できます。

## <a name="key-concepts"></a>主要な概念

Azure Functions でのデプロイの動作を理解するために重要な概念がいくつかあります。

### <a name="trigger-syncing"></a>トリガーの同期

トリガーのいずれかを変更する場合、Functions インフラストラクチャにその変更を認識させる必要があります。 同期は、多くのデプロイ テクノロジでは自動的に実行されます。 ただし、場合によっては、トリガーを手動で同期する必要があります。 外部パッケージ URL、ローカル Git、クラウドの同期、または FTP を参照することで更新をデプロイする場合は、トリガーを手動で同期する必要があります。 次の 3 つの方法のいずれかでトリガーを同期できます。

* Azure portal で関数アプリを再起動する。
* [マスター キー](functions-bindings-http-webhook-trigger.md#authorization-keys)を使用して HTTP POST 要求を `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` に送信する。
* HTTP POST 要求を `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` に送信する。 プレースホルダーは、ご使用のサブスクリプション ID、リソース グループ名、および関数アプリの名前に置き換えてください。

### <a name="remote-build"></a>リモート ビルド

Azure Functions では、zip デプロイ後に受け取ったコードのビルドを自動的に実行できます。 これらのビルドの動作は、アプリが Windows と Linux のどちらで実行されているかによって若干異なります。 アプリが[パッケージから実行](run-functions-from-deployment-package.md)モードに設定されている場合、リモート ビルドは実行されません。 リモート ビルドの使用方法については、[「zip デプロイ](#zip-deploy)」を参照してください。

> [!NOTE]
> リモート ビルドで問題が発生している場合は、この機能が利用可能になった日 (2019 年8月1日) より前にアプリが作成されている可能性があります。 新しい関数アプリを作成するか、`az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` を実行して関数アプリを更新してみてください。 このコマンドを正常に実行するには、2 回試行することが必要な場合があります。

#### <a name="remote-build-on-windows"></a>Windows でのリモート ビルド

Windows 上で実行されるすべての関数アプリには、小規模な管理アプリ (SCM (または [Kudu](https://github.com/projectkudu/kudu)) サイト) があります。 このサイトで、Azure Functions のデプロイとビルドのロジックの多くが処理されます。

アプリが Windows にデプロイされると、`dotnet restore` (C#) や `npm install` (JavaScript) などの言語固有のコマンドが実行されます。

#### <a name="remote-build-on-linux"></a>Linux でのリモート ビルド

Linux でリモート ビルドを有効にするには、次の[アプリケーション設定](functions-how-to-use-azure-function-app-settings.md#settings)を行う必要があります。

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

既定では、[Azure Functions Core Tools](functions-run-local.md) と [Visual Studio Code 用の Azure Functions 拡張機能](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)の両方で、Linux へのデプロイ時にリモート ビルドが実行されます。 このため、どちらのツールでも、これらの設定は Azure で自動的に作成されます。

Linux 上でリモートでビルドされたアプリは、[デプロイ パッケージから実行されます](run-functions-from-deployment-package.md)。

##### <a name="consumption-plan"></a>従量課金プラン

従量課金プランで実行されている Linux 関数アプリには SCM/Kudu サイトがありません。これにより、デプロイ オプションが制限されます。 ただし、従量課金プランで実行されている Linux 上の関数アプリでは、リモート ビルドがサポートされます。

##### <a name="dedicated-and-premium-plans"></a>専用プランと Premium プラン

[専用 (App Service) プラン](dedicated-plan.md)と [Premium プラン](functions-premium-plan.md)で実行されている Linux 上の関数アプリには、制限された SCM/Kudu サイトがあります。

## <a name="deployment-technology-details"></a>デプロイ テクノロジの詳細

Azure Functions では、次のデプロイ方法が使用できます。

### <a name="external-package-url"></a>外部パッケージ URL

外部パッケージ URL を使用して、関数アプリが含まれるリモート パッケージ (.zip) ファイルを参照できます。 このファイルは、指定の URL からダウンロードされます。アプリは [Run From Package](run-functions-from-deployment-package.md) モードで実行されます。

>__使用方法:__ アプリケーションの設定に [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) を追加します。 この設定では、値として URL (実行する特定のパッケージ ファイルの場所) を指定する必要があります。 [ポータルで](functions-how-to-use-azure-function-app-settings.md#settings)、または [Azure CLI を使用して](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)設定を追加できます。
>
>Azure Blob Storage を使用する場合は、[Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) を備えたプライベート コンテナーを使用して、Functions にパッケージへのアクセス権を付与します。 アプリケーションが再起動されるたびに、コンテンツのコピーがフェッチされます。 アプリケーションの有効期間中は、参照が有効である必要があります。

>__いつ使用するか:__ ユーザーが [リモート ビルド](#remote-build)の発生を望まない場合、従量課金プランの Linux 上で実行される Azure Functions でサポートされるデプロイ方法は、外部パッケージ URL のみになります。 関数アプリが参照しているパッケージ ファイルを更新する場合、Azure にアプリケーションが変更されたことを通知するために、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="zip-deploy"></a>ZIP デプロイ

ZIP デプロイを使用して、関数アプリが含まれる ZIP ファイルを Azure にプッシュします。 必要に応じて、[パッケージから実行](run-functions-from-deployment-package.md)を開始するようにアプリを設定するか、[リモート ビルド](#remote-build)を実行するように指定することができます。

>__使用方法:__ 次のお気に入りのクライアント ツールを使用してデプロイします。[Visual Studio Code](functions-develop-vs-code.md#publish-to-azure)、[Visual Studio](functions-develop-vs.md#publish-to-azure)、またはコマンド ラインを使用した [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)。 既定では、これらのツールは zip デプロイを使用し、[パッケージから実行](run-functions-from-deployment-package.md)されます。 Core Tools と Visual Studio Code 拡張機能の両方で、Linux へのデプロイ時に[リモート ビルド](#remote-build)が有効になります。 .zip ファイルを関数アプリに手動でデプロイするには、[.zip ファイルまたは URL からのデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)に関する記事の指示に従います。

>zip デプロイを使用してデプロイする場合は、[パッケージから実行](run-functions-from-deployment-package.md) するようにアプリを設定できます。 パッケージから実行するには、[`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) アプリケーション設定値を `1` に設定します。 ZIP デプロイをお勧めします。 これによりアプリケーションの読み込み時間が短縮されます。これは VS Code、Visual Studio、および Azure CLI の既定値になります。

>__いつ使用するか:__ zip デプロイは、Azure Functions で推奨されるデプロイ テクノロジです。

### <a name="docker-container"></a>Docker コンテナー

関数アプリが含まれる Linux コンテナー イメージをデプロイできます。

>__使用方法:__ Premium プランまたは専用プランで Linux 関数アプリを作成し、実行元のコンテナー イメージを指定します。 次の 2 つの方法で行います。
>
>* Azure portal を使用して、Azure App Service プランで Linux 関数アプリを作成します。 **[発行]** で、 **[Docker イメージ]** を選択し、コンテナーを構成します。 イメージがホストされている場所を入力します。
>* Azure CLI を使用して、App Service プランで Linux 関数アプリを作成します。 作成方法については、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)」をご覧ください。
>
>カスタム コンテナーを使用して既存のアプリにデプロイするには、[Azure Functions Core Tools](functions-run-local.md) で [`func deploy`](functions-run-local.md#publish) コマンドを使用します。

>__いつ使用するか:__ Docker コンテナー オプションは、関数アプリが実行される Linux 環境をより詳細に制御する必要がある場合に使用します。 このデプロイ メカニズムは、Linux 上で実行されている Functions に対してのみ使用できます。

### <a name="web-deploy-msdeploy"></a>Web デプロイ (MSDeploy)

Web デプロイは、Azure の Windows で実行される関数アプリを含む Windows アプリケーションをパッケージ化し、IIS サーバーにデプロイします。

>__使用方法:__ [Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md) を使用します。 **[パッケージ ファイルから実行します (推奨)]** チェック ボックスをオフにします。
>
>また、[Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) をダウンロードし、`MSDeploy.exe` を直接呼び出すこともできます。

>__いつ使用するか:__ Web デプロイはサポートされており、問題はありませんが、推奨されているメカニズムは、[[パッケージから実行する] が有効化された ZIP デプロイ](#zip-deploy)です。 詳細については、[Visual Studio デプロイ ガイド](functions-develop-vs.md#publish-to-azure)に関する記事をご覧ください。

### <a name="source-control"></a>ソース管理

ソース管理を使用して、関数アプリを Git リポジトリに接続します。 そのリポジトリ内のコードへの更新が、デプロイをトリガーします。 詳細については、「[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)」をご覧ください。

>__使用方法:__ ポータルの Functions 領域でデプロイ センターを使用して、ソース管理からのパブリッシュを設定します。 詳細については、「[Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」を参照してください。

>__いつ使用するか:__ 関数アプリで共同作業を行うチームにとっては、ソース管理を使用することがベスト プラクティスです。 ソース管理は、より高度なデプロイ パイプラインを可能にする優れたデプロイ オプションです。

### <a name="local-git"></a>ローカル Git

ローカル Git は、Git を使用して、ローカル コンピューターから Azure Functions にコードをプッシュするのに使用できます。

>__使用方法:__ 「[Azure App Service へのローカル Git デプロイ](../app-service/deploy-local-git.md)」の指示に従ってください。

>__いつ使用するか:__ 一般に、別のデプロイ方法を使用することをお勧めします。 ローカル Git からパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="cloud-sync"></a>クラウドの同期

Dropbox および OneDrive から Azure Functions にコンテンツを同期するには、クラウドの同期を使用します。

>__使用方法:__ 「[クラウド フォルダーからのコンテンツを同期する](../app-service/deploy-content-sync.md)」の指示に従ってください。

>__いつ使用するか:__ 一般に、他のデプロイ方法をお勧めします。 クラウドの同期を使用してパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="ftp"></a>FTP

FTP を使用して、ファイルを Azure Functions に直接転送できます。

>__使用方法:__ [FTP/S を使用したコンテンツのデプロイ](../app-service/deploy-ftp.md)の指示に従います。

>__いつ使用するか:__ 一般に、他のデプロイ方法をお勧めします。 FTP を使用してパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="portal-editing"></a>ポータルでの編集

ポータルベースのエディターでは、関数アプリ内のファイルを直接編集できます (基本的には、変更内容を保存するたびにデプロイされます)。

>__使用方法:__ Azure portal で関数を編集できるようにするために、[ポータルで関数を作成](./functions-get-started.md)しておく必要があります。 単一の信頼できるソースを保持するため、他のデプロイ方法を使用して、関数を読み取り専用にし、ポータルで編集できないようにします。 Azure portal でファイルを編集できる状態に戻すには、編集モードを `Read/Write` に手動で戻し、デプロイ関連のアプリケーション設定 ([`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) など) をすべて削除します。

>__いつ使用するか:__ Azure Functions の使用を開始するには、ポータルが適しています。 より集中的な開発作業には、次のクライアント ツールのいずれかを使用することをお勧めします。
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (コマンド ライン)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

次の表に、ポータルでの編集をサポートしているオペレーティング システムと言語を示します。

| Language | Windows Consumption | Windows Premium | Windows Dedicated | Linux Consumption | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# スクリプト |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js)JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (プレビュー) | | | | | | |
| PowerShell (プレビュー) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> ポータルでの編集は、Premium プランと専用プランを使用する Linux 上の Functions の HTTP トリガーとタイマー トリガーに対してのみ使用できます。

## <a name="deployment-behaviors"></a>デプロイ動作

デプロイすると、すべての既存実行に完了またはタイムアウトが許可されます。その後、新しいコードが読み込まれ、要求の処理が開始されます。

この移行をさらに制御する必要がある場合、デプロイ スロットを使用してください。

## <a name="deployment-slots"></a>デプロイ スロット

関数アプリを Azure にデプロイする場合、運用環境に直接デプロイする代わりに、個別のデプロイ スロットにデプロイできます。 デプロイ スロットの詳細については、[Azure Functions のデプロイ スロット](functions-deployment-slots.md)のドキュメントをご覧ください。

## <a name="next-steps"></a>次のステップ

関数アプリのデプロイの詳細については、次の記事を参照してください。

+ [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)
+ [Azure DevOps を使用した継続的デリバリー](functions-how-to-azure-devops.md)
+ [Azure Functions の ZIP デプロイ](deployment-zip-push.md)
+ [Azure Functions をパッケージ ファイルから実行する](run-functions-from-deployment-package.md)
+ [Azure Functions の関数アプリのリソース デプロイを自動化](functions-infrastructure-as-code.md)
