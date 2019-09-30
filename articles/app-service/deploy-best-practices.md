---
title: デプロイのベスト プラクティス - Azure App Service | Microsoft Docs
description: Azure App Service へのデプロイの主要なコンポーネントについて説明します。
keywords: Azure App Service, Web アプリ, デプロイする, デプロイ, パイプライン, ビルド
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: d1b6444b8512b1b55ac46370e805f8f662f5f555
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070680"
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

## <a name="language-specific-considerations"></a>言語固有の考慮事項

### <a name="java"></a>Java

JAR アプリケーションのデプロイには Kudu [zipdeploy/](deploy-zip.md) API を使用し、WAR アプリには [wardeploy/](deploy-zip.md#deploy-war-file) を使用してください。 Jenkins を使用している場合は、デプロイ フェーズでこれらの API を直接使用できます。 詳細については、 [こちらの記事](../jenkins/execute-cli-jenkins-pipeline.md)を参照してください。

### <a name="node"></a>ノード

既定では、Kudu は Node アプリケーションのビルド ステップ (`npm install`) を実行します。 Azure DevOps などのビルド サービスを使用している場合、Kudu ビルドは不要です。 Kudu ビルドを無効にするには、`false` の値を指定して `SCM_DO_BUILD_DURING_DEPLOYMENT` というアプリ設定を作成します。

### <a name="net"></a>.NET 

既定では、Kudu は .Net アプリケーションのビルド ステップ (`dotnet build`) を実行します。 Azure DevOps などのビルド サービスを使用している場合、Kudu ビルドは不要です。 Kudu ビルドを無効にするには、`false` の値を指定して `SCM_DO_BUILD_DURING_DEPLOYMENT` というアプリ設定を作成します。

## <a name="other-deployment-considerations"></a>デプロイに関するその他の考慮事項

### <a name="use-deployment-slots"></a>デプロイ スロットの使用

新しい運用ビルドをデプロイするときは、可能な限り、[デプロイ スロット](deploy-staging-slots.md)を使用してください。 Standard App Service プラン レベル以上を使用している場合は、ステージング環境へのアプリのデプロイ、変更の検証、スモーク テストを行うことができます。 準備ができたら、ステージング スロットと運用スロットをスワップできます。 スワップ操作によって、必要なワーカー インスタンスが運用規模に合わせてウォームアップされるため、ダウンタイムがなくなります。 

### <a name="local-cache"></a>ローカル キャッシュ

Azure App Service のコンテンツは Azure Storage に保存され、コンテンツ共有として永続的な方法で表示されます。 ただし、アプリによっては、高可用で実行可能な高パフォーマンスの読み取り専用コンテンツ ストアのみが必要になる場合があります。 こうしたアプリは、[ローカル キャッシュ](overview-local-cache.md)を使うことでメリットが得られます。 WordPress などのコンテンツ管理サイトには、ローカル キャッシュはお勧めしません。

ダウンタイムを防ぐために、必ず [デプロイ スロット] (deploy-staging-slots md) と共にローカル キャッシュを使用してください。 これらの機能を一緒に使用する方法については、[こちらのセクション](overview-local-cache.md#best-practices-for-using-app-service-local-cache)を参照してください。

### <a name="high-cpu-or-memory"></a>CPU またはメモリの高い使用率

App Service プランで、使用可能な CPU またはメモリの 90% 以上が使用されている場合、基になる仮想マシンでのデプロイの処理に問題が発生する可能性があります。 これが発生している場合は、デプロイを実行するためにインスタンス数を一時的にスケールアップしてください。 デプロイが完了したら、インスタンス数を前の値に戻すことができます。
