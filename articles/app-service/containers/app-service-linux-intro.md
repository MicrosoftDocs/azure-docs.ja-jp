---
title: App Service on Linux の概要 - Azure | Microsoft Docs
description: Azure App Service on Linux について説明します。
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 22c4096711bbc1d47ff6684e38ac829d77681a9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793443"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Azure App Service on Linux の概要

[Azure App Service](../overview.md) は、Web サイトと Web アプリケーションをホストするために最適化された、フル マネージドのコンピューティング プラットフォームです。 顧客は App Service on Linux を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。 「[言語](#languages)」セクションに、現在サポートされているアプリケーション スタックの一覧を示します。

## <a name="languages"></a>Languages

App Service on Linux では、開発者の生産性を向上させるために、多数の組み込みイメージがサポートされています。 アプリケーションに必要なランタイムが組み込みイメージでサポートされていない場合は、[独自の Docker イメージを作成](tutorial-custom-docker-image.md)して Web App for Containers にデプロイする方法があります。

| 言語 | サポートされているバージョン |
|---|---|
| Node.js | 4.4、4.5、4.8、6.2、6.6、6.9、6.10、6.11、8.0、8.1、8.2、8.8、8.9、8.11、9.4、10.1、10.10 |
| Java * | Tomcat 8.5、9.0、Java SE、WildFly 14 (いずれも JRE 8 を実行) |
| PHP | 5.6、7.0、7.2 |
| Python (プレビュー) | 2.7、3.6、3.7 |
| .NET Core | 1.0、1.1、2.0、2.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>デプロイメント

* FTP
* ローカル Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* ステージング環境
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) と DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>コンソール、公開、およびデバッグ

* 環境
* デプロイメント
* 基本コンソール
* SSH

## <a name="scaling"></a>スケーリング

* [App Service プラン](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)のレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。

## <a name="locations"></a>Locations

[Azure ステータス ダッシュボード](https://azure.microsoft.com/status)を確認します。

## <a name="limitations"></a>制限事項

Azure Portal では、Web App for Containers で現在使用できる機能のみが表示されます。 他の機能は、有効になったときにポータルに表示されるようになります。

App Service on Linux は [Basic、Standard、および Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service プランでのみサポートされており、[Free または Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) レベルはありません。 Web App for Containers は、非 Linux Web Apps で既にホストされている App Service プランでは作成できません。  

現在の制限に基づき、Windows アプリと Linux アプリを同じリソース グループに混在させることはできません。

## <a name="troubleshooting"></a>トラブルシューティング

アプリケーションが起動しない場合、またはアプリのログ記録を調べる場合は、LogFiles ディレクトリの Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。
コンテナーから `stdout` および `stderr` をログ記録するには、**[診断ログ]** で **[Docker Container ログ]** を有効にする必要があります。

![ログ記録の有効化][2]

設定はすぐに有効になります。 App Service により設定の変更が検出され、自動的にコンテナーが再起動されます。

SCM サイトには、**[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

![Kudu を使用した Docker のログの表示][1]

## <a name="next-steps"></a>次の手順

以下の記事では、さまざまな言語で記述した Web アプリを使用して App Service on Linux での作業を開始することができます。

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [複数コンテナー アプリ](quickstart-multi-container.md)

App Service on Linux の詳細については、次のページを参照してください。

* [App Service on Linux の FAQ](app-service-linux-faq.md)
* [App Service on Linux での SSH のサポート](app-service-linux-ssh-support.md)
* [App Service でステージング環境を設定する](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub の継続的なデプロイ](app-service-linux-ci-cd.md)

質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
