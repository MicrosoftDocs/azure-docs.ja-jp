---
title: "Azure Web App for Containers の概要 | Microsoft Docs"
description: "Azure Web App for Containers について説明します。"
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: bdfaf0cd7d4bb44b877a9f14891e540fd789b6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-web-app-for-containers"></a>Azure Web App for Containers の概要

[Web App](../app-service-web-overview.md) は、Web サイトと Web アプリケーションをホストするために最適化された、完全に管理されたコンピューティング プラットフォームです。 Web App for Containers を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。 次のセクションは、現在サポートされているアプリケーション スタックの一覧です。

## <a name="languages"></a>言語

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

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

* [App Service プラン](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)のレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。

## <a name="locations"></a>場所

[Azure ステータス ダッシュボード](https://azure.microsoft.com/status)を確認します。

## <a name="limitations"></a>制限事項

Azure Portal では、Web App for Containers で現在使用できる機能のみが表示されます。 他の機能は、有効になったときにポータルに表示されるようになります。

仮想ネットワーク統合、Azure Active Directory/サード パーティの認証、Kudu サイト拡張機能などの機能は、まだ利用できません。 これらの機能が利用可能になったら、ドキュメントとブログを更新し、変更についてお知らせします。

Web App for Containers は [Basic および Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service プランでのみサポートされており、[Free または Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) レベルはありません。 Web App for Containers には、次の重要な制限もあります。

* Web App for Containers は、非 Linux Web Apps で既にホストされている App Service プランでは作成できません。
* Web App for Containers を非 Linux Web Apps が含まれているリソース グループに作成する場合は、既存の App Service プランとは別のリージョンに App Service プランを作成する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

アプリケーションが起動しない場合、またはアプリのログ記録を調べる場合は、LogFiles ディレクトリの Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。
コンテナーから `stdout` および `stderr` をログ記録するには、**[診断ログ]** で **[Docker Container ログ]** を有効にする必要があります。

![ログ記録の有効化][2]

![Kudu を使用した Docker のログの表示][1]

SCM サイトには、**[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

## <a name="next-steps"></a>次のステップ

App Service on Linux の使用を開始するには、次のリンクを参照してください。 質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

* [Azure Web App for Containers のカスタム Docker イメージを使用する方法](quickstart-custom-docker-image.md)
* [Azure App Service Web App for Containers での .NET Core の使用](quickstart-dotnetcore.md)
* [Azure App Service Web App for Containers での Ruby の使用](quickstart-ruby.md)
* [Azure App Service Web App for Containers の FAQ](app-service-linux-faq.md)
* [Azure Web App for Containers での SSH のサポート](app-service-linux-ssh-support.md)
* [Azure App Service でステージング環境を設定する](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Azure Web App for Containers による Docker ハブの継続的なデプロイ](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
