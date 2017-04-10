---
title: "Azure での New Relic の使用 | Microsoft Docs"
description: "New Relic サービスを使用して Azure アプリケーションを管理および監視する方法について説明します。"
services: 
documentationcenter: .net
author: nickfloyd
manager: timlt
editor: 
ms.assetid: b01011be-c344-4e33-987d-c93dac1971fb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2016
ms.author: nickfloyd@newrelic.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4f13c909a6ff640d403f5264004176c087925dd


---
# <a name="new-relic-application-performance-management-on-azure"></a>Azure の New Relic によるアプリケーション パフォーマンス管理
Azure でホストされるアプリケーションに、New Relic による世界クラスのパフォーマンス監視を追加することができます。 Azure アプリの監視、トラブルシューティング、チューニングを包括的に行えるようになるほか、Azure を使用することで New Relic 製品の割引価格の対象にもなります。

## <a name="what-is-new-relic"></a>New Relic とは
[New Relic 製品](https://newrelic.com/products)を使用すると、アプリケーション エラーを解決し、潜在的な問題を早期に発見し、環境全体のパフォーマンスを把握することができます。 パフォーマンス上の問題を特定して診断する時間を短縮できるように設計されており、その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、サーバーとユーザーのブラウザーの両方から追跡します。 データベースでの処理にかかった時間の表示、時間がかかったクエリと Web 要求の分析、アップタイムの監視と通知、アプリケーションの例外の追跡など、多数の処理を実行します。 

## <a name="special-pricing"></a>特別価格
New Relic Standard は、Azure ユーザーであれば無料でご利用いただけます。 New Relic Pro は、Azure Cloud Services のインスタンス サイズに基づいて提供されます。 価格情報については、Azure Marketplace の [New Relic のページ](https://azure.microsoft.com/marketplace/partners/newrelic/newrelic/)を参照してください。また、エンタープライズレベルの価格については、New Relic ((sales@newrelic.com)) にお問い合わせください。

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用版サブスクリプションをご利用になれます。

## <a name="sign-up-for-new-relic-using-the-azure-store"></a>Azure ストアを使用して New Relic にサインアップする
New Relic は、Azure の Web ロールおよびワーカー ロールとシームレスに統合されます。 Azure Store から直接、New Relic にすばやく簡単にサインアップできます。 手順については、 [Azure Store でのサインアップ手順](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services#signup) に関する New Relic のページを参照してください。

## <a name="view-your-data"></a>データの表示
サインアップすると、New Relic のすばらしいアプリケーション監視とデータ主導の分析を活用することができます。 New Relic でアプリのパフォーマンスを確認するには、次の手順を実行します。

1. Azure Portal で [管理] を選択します。
2. New Relic アカウントの電子メールとパスワードを使用してサインインします。
3. [APM の [Overview (概要)] ページ](https://docs.newrelic.com/docs/apm/applications-menu/monitoring/apm-overview-page)で、すべてのデータを表示するアプリケーションをインデックスから選択します。

## <a name="using-new-relic-with-azure"></a>Azure での New Relic の使用
Azure での New Relic の使用の詳細については、以下の情報を含む [New Relic のドキュメントのサイト](https://docs.newrelic.com/docs/agents/net-agent/azure-installation)を参照してください。 

* [.NET 用の New Relic](https://docs.newrelic.com/docs/agents/net-agent/getting-started/new-relic-net)
* [Azure クラウド サービスでの .NET worker ロールのインストルメント化](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/instrument-net-worker-role-azure-cloud-service)
* [Microsoft Azure クラウド プラットフォーム用の New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services)
* [Microsoft Azure App Service 用の New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-portal)
* [New Relic/Azure のトラブルシューティング](https://docs.newrelic.com/docs/agents/net-agent/azure-troubleshooting)




<!--HONumber=Nov16_HO3-->


