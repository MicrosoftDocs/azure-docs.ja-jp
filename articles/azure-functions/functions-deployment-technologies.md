---
title: Azure Functions のデプロイ テクノロジ | Microsoft Docs
description: Azure Functions にコードをデプロイするさまざまな方法について学習します。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594390"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions のデプロイ テクノロジ

各種のテクノロジを使用して、Azure Functions プロジェクト コードを Azure にデプロイすることができます。 この記事では、これらのテクノロジの包括的な一覧を提供し、どのテクノロジをどの種類の関数に対して使用できるかを説明し、各手法を使用するとどのようなことが起こるかについて説明し、各種シナリオで使用する最適な手法についてレコメンデーションを提示します。 Azure Functions へのデプロイをサポートする各種ツールは、それらのコンテキストに基づいて適切なテクノロジとなるように調整されています。

## <a name="deployment-technology-availability"></a>デプロイ テクノロジの利用可否

> [!IMPORTANT]
> Azure Functions は、クロス プラットフォームのローカル開発と、Windows と Linux でのホスティングをサポートしています。 現時点では、次の 3 つのホスティング プランを利用できます。[従量課金](functions-scale.md#consumption-plan)、[Premium](functions-scale.md#premium-plan)、[Dedicated (Azure App Service)](functions-scale.md#app-service-plan) です。 各プランの動作は異なります。 各種の Azure Functions に対してすべてのデプロイ テクノロジが使用できるわけではありません。

| デプロイ テクノロジ | Windows Consumption | Windows Premium (プレビュー) | Windows Dedicated  | Linux 従量課金 (プレビュー) | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 外部パッケージ URL<sup>1</sup> |✔|✔|✔|✔|✔|
| ZIP デプロイ |✔|✔|✔| |✔|
| Docker コンテナー | | | | |✔|
| Web デプロイ |✔|✔|✔| | |
| ソース管理 |✔|✔|✔| |✔|
| ローカル Git<sup>1</sup> |✔|✔|✔| |✔|
| クラウドの同期<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| ポータルでの編集 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> [トリガーの手動同期](#trigger-syncing)が必要なデプロイ テクノロジ。  
<sup>2</sup> ポータルでの編集は、Dedicated プランを使用する Linux 上の Functions の HTTP トリガーとタイマー トリガーに対してのみ使用できます。

## <a name="key-concepts"></a>主要な概念

Azure Functions でのデプロイの動作を理解するために重要な概念がいくつかあります。

### <a name="trigger-syncing"></a>トリガーの同期

トリガーのいずれかを変更する場合、Functions インフラストラクチャにその変更を認識させる必要があります。 同期は、多くのデプロイ テクノロジでは自動的に実行されます。 ただし、場合によっては、トリガーを手動で同期する必要があります。 外部パッケージ URL、ローカル Git、クラウドの同期、または FTP を参照することで更新をデプロイする場合は、トリガーを手動で同期する必要があります。 次の 3 つの方法のいずれかでトリガーを同期できます。

* Azure portal で関数アプリを再起動する。
* [マスター キー](functions-bindings-http-webhook.md#authorization-keys)を使用して HTTP POST 要求を `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` に送信する。
* HTTP POST 要求を `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` に送信する。 プレースホルダーは、ご使用のサブスクリプション ID、リソース グループ名、および関数アプリの名前に置き換えてください。

## <a name="deployment-technology-details"></a>デプロイ テクノロジの詳細 

Azure Functions では、次のデプロイ方法が使用できます。

### <a name="external-package-url"></a>外部パッケージ URL

外部パッケージ URL を使用して、関数アプリが含まれるリモート パッケージ (.zip) ファイルを参照できます。 このファイルは、指定の URL からダウンロードされます。アプリは [Run From Package](run-functions-from-deployment-package.md) モードで実行されます。

>__使用方法:__ アプリケーションの設定に `WEBSITE_RUN_FROM_PACKAGE` を追加します。 この設定では、値として URL (実行する特定のパッケージ ファイルの場所) を指定する必要があります。 [ポータルで](functions-how-to-use-azure-function-app-settings.md#settings)、または [Azure CLI を使用して](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)設定を追加できます。 
>
>Azure Blob Storage を使用する場合は、[Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) を備えたプライベート コンテナーを使用して、Functions にパッケージへのアクセス権を付与します。 アプリケーションが再起動されるたびに、コンテンツのコピーがフェッチされます。 アプリケーションの有効期間中は、参照が有効である必要があります。

>__いつ使用するか:__ 外部パッケージ URL は、従量課金プラン (プレビュー) の Linux で実行される Azure Functions に対してサポートされる唯一のデプロイ方法です。 関数アプリが参照しているパッケージ ファイルを更新する場合、Azure にアプリケーションが変更されたことを通知するために、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="zip-deploy"></a>ZIP デプロイ

ZIP デプロイを使用して、関数アプリが含まれる ZIP ファイルを Azure にプッシュします。 必要に応じて、[Run From Package](run-functions-from-deployment-package.md) モードで起動するようにアプリを設定できます。

>__使用方法:__ 次のお気に入りのクライアント ツールを使用してデプロイします。[VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)、[Visual Studio](functions-develop-vs.md#publish-to-azure)、または [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 .zip ファイルを関数アプリに手動でデプロイするには、[.zip ファイルまたは URL からのデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)に関する記事の指示に従います。
>
>ZIP デプロイを使用してデプロイする場合は、[Run From Package](run-functions-from-deployment-package.md) モードで実行されるようにアプリを設定できます。 Run From Package モードを設定するには、`WEBSITE_RUN_FROM_PACKAGE` アプリケーションの設定値を `1` に設定します。 ZIP デプロイをお勧めします。 これによりアプリケーションの読み込み時間が短縮されます。これは VS Code、Visual Studio、および Azure CLI の既定値になります。

>__いつ使用するか:__ ZIP デプロイは、Windows で実行される Azure Functions と Linux で実行される Azure Functions (Dedicated プラン) に対して推奨されるデプロイ テクノロジです。

### <a name="docker-container"></a>Docker コンテナー

関数アプリが含まれる Linux コンテナー イメージをデプロイできます。

>__使用方法:__ Dedicated プランで Linux 関数アプリを作成し、実行元のコンテナー イメージを指定します。 次の 2 つの方法で行います。
>
>* Azure portal を使用して、Azure App Service プランで Linux 関数アプリを作成します。 **[発行]** で、 **[Docker イメージ]** を選択し、コンテナーを構成します。 イメージがホストされている場所を入力します。
>* Azure CLI を使用して、App Service プランで Linux 関数アプリを作成します。 作成方法については、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)」をご覧ください。
>
>カスタム コンテナーを使用して既存のアプリにデプロイするには、[Azure Functions Core Tools](functions-run-local.md) で [`func deploy`](functions-run-local.md#publish) コマンドを使用します。

>__いつ使用するか:__ Docker コンテナー オプションは、関数アプリが実行される Linux 環境をより詳細に制御する必要がある場合に使用します。 このデプロイ メカニズムは、App Service プランの Linux で実行されている Functions に対してのみ使用できます。

### <a name="web-deploy-msdeploy"></a>Web デプロイ (MSDeploy)

Web デプロイは、Azure の Windows で実行される関数アプリを含む Windows アプリケーションをパッケージ化し、IIS サーバーにデプロイします。

>__使用方法:__ [Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md) を使用します。 **[パッケージ ファイルから実行します (推奨)]** チェック ボックスをオフにします。
>
>また、[Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) をダウンロードし、`MSDeploy.exe` を直接呼び出すこともできます。

>__いつ使用するか:__ Web デプロイはサポートされており、問題はありませんが、推奨されているメカニズムは、[[パッケージから実行する] が有効化された ZIP デプロイ](#zip-deploy)です。 詳細については、[Visual Studio デプロイ ガイド](functions-develop-vs.md#publish-to-azure)に関する記事をご覧ください。

### <a name="source-control"></a>ソース管理

ソース管理を使用して、関数アプリを Git リポジトリに接続します。 そのリポジトリ内のコードへの更新が、デプロイをトリガーします。 詳細については、「[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)」をご覧ください。

>__使用方法:__ Azure Functions ポータルでデプロイ センターを使用して、ソース管理からのパブリッシュを設定します。 詳細については、「[Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」を参照してください。

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

>__使用方法:__ Azure portal で関数を編集できるようにするために、[ポータルで関数を作成](functions-create-first-azure-function.md)しておく必要があります。 単一の信頼できるソースを保持するため、他のデプロイ方法を使用して、関数を読み取り専用にし、ポータルで編集できないようにします。 Azure portal でファイルを編集できる状態に戻すには、編集モードを `Read/Write` に手動で戻し、デプロイ関連のアプリケーション設定 (`WEBSITE_RUN_FROM_PACKAGE` など) をすべて削除します。 

>__いつ使用するか:__ Azure Functions の使用を開始するには、ポータルが適しています。 より集中的な開発作業には、クライアント ツールを使用することをお勧めします。
>
>* [VS Code を使用して開始する](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools を使用して開始する](functions-run-local.md)
>* [Visual Studio を使用して開始する](functions-create-your-first-function-visual-studio.md)

次の表に、ポータルでの編集をサポートしているオペレーティング システムと言語を示します。

| | Windows Consumption | Windows Premium (プレビュー) | Windows Dedicated | Linux Consumption (プレビュー) | Linux Dedicated |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# スクリプト |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js)JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (プレビュー) | | | | | |
| PowerShell (プレビュー) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> ポータルでの編集は、Dedicated プランを使用する Linux 上の Functions の HTTP トリガーとタイマー トリガーに対してのみ使用できます。

## <a name="deployment-slots"></a>デプロイ スロット

関数アプリを Azure にデプロイする場合、運用環境に直接デプロイする代わりに、個別のデプロイ スロットにデプロイできます。 デプロイ スロットの詳細については、[Azure App Service スロット](../app-service/deploy-staging-slots.md)に関するページをご覧ください。

### <a name="deployment-slots-levels-of-support"></a>デプロイ スロットのサポートのレベル

デプロイ スロットには、次の 2 つのレベルのサポートがあります。

* **一般公開 (GA)** :完全にサポートされ、運用環境用に承認されています。
* **プレビュー**:まだサポートされていませんが、今後 GA 状態に達すると想定されています。

| OS/ホスティング プラン | サポートのレベル |
| --------------- | ------ |
| Windows Consumption | プレビュー |
| Windows Premium (プレビュー) | プレビュー |
| Windows Dedicated | 一般公開 |
| Linux Consumption | サポートされていません |
| Linux Dedicated | 一般公開 |

## <a name="next-steps"></a>次の手順

関数アプリのデプロイの詳細については、次の記事を参照してください。 

+ [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)
+ [Azure DevOps を使用した継続的デリバリー](functions-how-to-azure-devops.md)
+ [Azure Functions の ZIP デプロイ](deployment-zip-push.md)
+ [Azure Functions をパッケージ ファイルから実行する](run-functions-from-deployment-package.md)
+ [Azure Functions の関数アプリのリソース デプロイを自動化](functions-infrastructure-as-code.md)
