---
title: デプロイのベスト プラクティス
description: Azure App Service へのデプロイの主要なメカニズムについて説明します。 言語固有の推奨事項とその他の注意事項を検索できます。
keywords: Azure App Service, Web アプリ, デプロイする, デプロイ, パイプライン, ビルド
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: ac4e8d93e2e729db387b0f9cf81017b6c58b6750
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951336"
---
# <a name="deployment-best-practices"></a>デプロイのベスト プラクティス

開発チームごとに独自の要件があり、それによって、クラウド サービスで効率的なデプロイ パイプラインの実装が困難になる可能性があります。 この記事では、App Service へのデプロイにおける 3 つの主要なコンポーネントである、デプロイ ソース、ビルド パイプライン、およびデプロイ メカニズムについて説明します。 この記事では、特定の言語スタックのベスト プラクティスとヒントについても紹介します。

## <a name="deployment-components"></a>デプロイ コンポーネント

### <a name="deployment-source"></a>デプロイ ソース

デプロイ ソースは、アプリケーション コードの場所です。 運用アプリの場合、デプロイ ソースは通常、[GitHub、BitBucket、Azure Repos](deploy-continuous-deployment.md) などのバージョン管理ソフトウェアによってホストされるリポジトリです。 開発とテストのシナリオでは、デプロイ ソースは[ローカル コンピューター上のプロジェクト](deploy-local-git.md)である可能性があります。 また、App Service は、 [OneDrive フォルダーと Dropbox フォルダー](deploy-content-sync.md)もデプロイ ソースとしてサポートしています。 クラウド フォルダーを使用すると、App Service を簡単に開始できますが、エンタープライズ レベルの実稼働アプリケーションにこのソースを使用することは、通常は推奨されません。 

### <a name="build-pipeline"></a>ビルド パイプライン

デプロイ ソースを決定したら、次の手順として、ビルド パイプラインを選択します。 ビルド パイプラインは、デプロイ ソースからソース コードを読み取り、一連の手順 (コードのコンパイル、HTML と JavaScript の縮小、テストの実行、コンポーネントのパッケージ化など) を実行して、アプリケーションを実行可能な状態にします。 ビルド パイプラインによって実行される特定のコマンドは、言語スタックによって異なります。 これらの操作は、Azure Pipelines などのビルド サーバーで実行することも、ローカルで実行することもできます。

### <a name="deployment-mechanism"></a>デプロイ メカニズム

デプロイ メカニズムは、ビルドしたアプリケーションを Web アプリの */home/site/wwwroot* ディレクトリに配置するために使用されるアクションです。 */wwwroot* ディレクトリは、Web アプリのすべてのインスタンスによって共有される、マウントされたストレージの場所です。 デプロイ メカニズムによってアプリケーションがこのディレクトリに配置されると、インスタンスは新しいファイルを同期するための通知を受け取ります。 App Service では、次のデプロイ メカニズムをサポートしています。

- Kudu エンドポイント:[Kudu](https://github.com/projectkudu/kudu/wiki) は、Windows App Service 内では独立したプロセスとして実行され、Linux App Service 内では 2 番目のコンテナーとして実行される、オープンソースの開発者生産性ツールです。 Kudu は、継続的なデプロイを処理し、zipdeploy などのデプロイ用の HTTP エンドポイントを提供します。
- FTP と WebDeploy:[サイトまたはユーザーの資格情報](deploy-configure-credentials.md)を使用して、[FTP 経由](deploy-ftp.md)または WebDeploy 経由でファイルをアップロードできます。 これらのメカニズムは、Kudu を経由しません。  

Azure Pipelines、Jenkins、エディター プラグインなどのデプロイ ツールでは、これらのデプロイ メカニズムのいずれかを使用します。

## <a name="use-deployment-slots"></a>デプロイ スロットの使用

新しい運用ビルドをデプロイするときは、可能な限り、[デプロイ スロット](deploy-staging-slots.md)を使用してください。 Standard App Service プラン レベル以上を使用している場合は、ステージング環境へのアプリのデプロイ、変更の検証、スモーク テストを行うことができます。 準備ができたら、ステージング スロットと運用スロットをスワップできます。 スワップ操作によって、必要なワーカー インスタンスが運用規模に合わせてウォームアップされるため、ダウンタイムがなくなります。

### <a name="continuously-deploy-code"></a>コードを継続的にデプロイする

テスト、QA、ステージング用に指定されたブランチがプロジェクトにある場合は、それらの各ブランチをステージング スロットに継続的にデプロイする必要があります。 (これは、[Gitflow 設計](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)と呼ばれています。)これにより、関係者は、デプロイされたブランチを簡単に評価してテストすることができます。 

運用スロットに対しては継続的デプロイを有効にしないでください。 代わりに、運用ブランチ (多くの場合、メイン) を非運用スロットにデプロイします。 ベース ブランチをリリースする準備ができたら、それを運用スロットにスワップします。 運用環境にデプロイするのではなく、運用環境にスワップすると、ダウンタイムの発生が抑えられ、もう一度スワップすることで変更をロールバックすることができます。 

![Dev、ステージング、およびメイン ブランチ間のフローとそれらのデプロイ先のスロットを示す図。](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>コンテナーを継続的にデプロイする

Docker やその他のコンテナー レジストリのカスタム コンテナーの場合は、イメージをステージング スロットにデプロイし、運用環境にスワップしてダウンタイムを回避します。 イメージをコンテナー レジストリにプッシュし、webapp でイメージ タグを更新する必要があるため、オートメーションは、コードのデプロイよりも複雑になります。

スロットにデプロイするブランチごとに、ブランチへの各コミットで次を実行するようにオートメーションを設定します。

1. **イメージをビルドしてタグ付けする**。 ビルド パイプラインの一部として、git のコミット ID、タイムスタンプ、またはその他の特定可能な情報でイメージにタグを付けます。 既定の "latest" タグは使用しないことをお勧めします。 そうしないと、現在デプロイされているコードの追跡が困難になり、デバッグがはるかに難しくなります。
1. **タグ付けされたイメージをプッシュする**。 イメージがビルドされてタグ付けされると、パイプラインはイメージをコンテナー レジストリにプッシュします。 次の手順では、デプロイ スロットが、タグ付けされたイメージをコンテナー レジストリからプルします。
1. **デプロイ スロットを新しいイメージ タグで更新する**。 このプロパティが更新されると、サイトは自動的に再起動して、新しいコンテナー イメージをプルします。

![スロットの使用状況の図](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

一般的なオートメーション フレームワークの例を以下に示します。

### <a name="use-azure-devops"></a>Azure DevOps を使用する

App Service には、デプロイ センターを介して、コンテナーの[組み込みの継続的デリバリー](deploy-continuous-deployment.md)があります。 [Azure portal](https://portal.azure.com/) でアプリに移動し、 **[デプロイ]** の下で **[デプロイ センター]** を選択します。 指示に従って、リポジトリとブランチを選択します。 これにより、選択したブランチに新しいコミットがプッシュされたときに、コンテナーを自動的にビルド、タグ付け、デプロイするように DevOps のビルドとリリースのパイプラインが構成されます。

### <a name="use-github-actions"></a>GitHub Actions を使用する

[GitHub Actions](./deploy-ci-cd-custom-container.md) を使用して、コンテナーのデプロイを自動化することもできます。  次のワークフロー ファイルは、コミット ID を使ってコンテナーをビルドしてタグ付けし、それをコンテナー レジストリにプッシュし、指定したサイト スロットを新しいイメージ タグで更新します。

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>他のオートメーション プロバイダーを使用する

前述の手順は、CircleCI や Travis CI などの他のオートメーション ユーティリティに適用されます。 ただし、最後の手順で、新しいイメージ タグでデプロイ スロットを更新するには、Azure CLI を使用する必要があります。 オートメーション スクリプトで Azure CLI を使用するには、次のコマンドを使用してサービス プリンシパルを生成します。

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

スクリプトで、`az login --service-principal` を使用し、プリンシパルの情報を指定してログインします。 次に、`az webapp config container set` を使用して、コンテナー名、タグ、レジストリ URL、レジストリ パスワードを設定します。 コンテナーの CI プロセスを構築するのに役立つリンクを次に示します。

- [Circle CI で Azure CLI にログインする方法](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>言語固有の考慮事項

### <a name="java"></a>Java

JAR アプリケーションのデプロイには Kudu [zipdeploy/](deploy-zip.md) API を使用し、WAR アプリには [wardeploy/](deploy-zip.md#deploy-war-file) を使用してください。 Jenkins を使用している場合は、デプロイ フェーズでこれらの API を直接使用できます。 詳細については、 [こちらの記事](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)を参照してください。

### <a name="node"></a>Node

既定では、Kudu は Node アプリケーションのビルド ステップ (`npm install`) を実行します。 Azure DevOps などのビルド サービスを使用している場合、Kudu ビルドは不要です。 Kudu ビルドを無効にするには、`false` の値を指定して `SCM_DO_BUILD_DURING_DEPLOYMENT` というアプリ設定を作成します。

### <a name="net"></a>.NET 

既定では、Kudu は .NET アプリケーションのビルド ステップ (`dotnet build`) を実行します。 Azure DevOps などのビルド サービスを使用している場合、Kudu ビルドは不要です。 Kudu ビルドを無効にするには、`false` の値を指定して `SCM_DO_BUILD_DURING_DEPLOYMENT` というアプリ設定を作成します。

## <a name="other-deployment-considerations"></a>デプロイに関するその他の考慮事項

### <a name="local-cache"></a>ローカル キャッシュ

Azure App Service のコンテンツは Azure Storage に保存され、コンテンツ共有として永続的な方法で表示されます。 ただし、アプリによっては、高可用で実行可能な高パフォーマンスの読み取り専用コンテンツ ストアのみが必要になる場合があります。 こうしたアプリは、[ローカル キャッシュ](overview-local-cache.md)を使うことでメリットが得られます。 WordPress などのコンテンツ管理サイトには、ローカル キャッシュはお勧めしません。

ダウンタイムを防ぐために、必ず[デプロイ スロット](deploy-staging-slots.md)と共にローカル キャッシュを使用してください。 これらの機能を一緒に使用する方法については、[こちらのセクション](overview-local-cache.md#best-practices-for-using-app-service-local-cache)を参照してください。

### <a name="high-cpu-or-memory"></a>CPU またはメモリの高い使用率

App Service プランで、使用可能な CPU またはメモリの 90% 以上が使用されている場合、基になる仮想マシンでのデプロイの処理に問題が発生する可能性があります。 これが発生している場合は、デプロイを実行するためにインスタンス数を一時的にスケールアップしてください。 デプロイが完了したら、インスタンス数を前の値に戻すことができます。

ベスト プラクティスの詳細については、[App Service 診断](./overview-diagnostics.md)にアクセスして、リソース専用の実行可能なベスト プラクティスを確認してください。

- [Azure portal](https://portal.azure.com) で Web App に移動します。
- 左側のナビゲーションで **[問題の診断と解決]** をクリックすると、App Service 診断が開きます。
- **[ベスト プラクティス]** ホームページ タイルを選択します。
- **[Best Practices for Availability & Performance]\(可用性 & パフォーマンスのベスト プラクティス\)** または **[Best Practices for Optimal Configuration]\(最適な構成のベスト プラクティス\)** をクリックして、これらのベスト プラクティスに関するアプリの現在の状態を表示します。

また、こちらのリンクを使用して、リソースの App Service 診断を直接開くこともできます: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。