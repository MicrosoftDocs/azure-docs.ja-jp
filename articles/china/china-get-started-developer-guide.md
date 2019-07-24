---
title: Azure China 21Vianet の開発者ガイド | Microsoft Docs
description: Microsoft では、開発者によるクラウド アプリケーションの作成と、グローバル Azure および Azure China 21Vianet へのデプロイを実行するのに役立つツールを提供しています。 グローバル Azure と Azure China の両方で利用できるサービスと機能、および中国では利用できない可能性がある機能についても説明します。
services: china
cloud: na
documentationcenter: na
author: v-wimarc
manager: edprice
ms.assetid: na
ms.service: china
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: v-wimarc
ms.openlocfilehash: b48add54e83eb2d30b40faf64bc1b5c2c769f237
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521677"
---
# <a name="developer-guide-for-azure-china-21vianet"></a>Azure China 21Vianet の開発者ガイド
Microsoft では、開発者によるクラウド アプリケーションの作成と、グローバルな Microsoft Azure サービス ("グローバル Azure") および 21Vianet (Azure China 21Vianet) が運営する Microsoft Azure へのデプロイを実行するのに役立つツールを提供しています。 [Azure ドキュメント センター](https://azure.microsoft.com/documentation/)など、現在利用可能な技術的コンテンツのほとんどは、アプリケーションがグローバル Azure 用に開発されることを前提としているため、どの[サービスおよび機能](https://www.azure.cn/home/features/products-by-region)が Microsoft Azure China 21Vianet で利用できるかを把握することが重要です。

グローバル Azure サービスの一般利用可能なコンテンツを参照する場合は必ず、手順を適合させるか、グローバル Azure サービスの設定を指定するサンプル コードをカスタマイズしてください。 たとえば、 [Azure サービス エンドポイント](#check-endpoints-in-azure)をカスタマイズします。

## <a name="migrate-existing-applications-and-workloads"></a>既存のアプリケーションとワークロードを移行する
他の場所でホストされているアプリケーションまたはワークロードの移行には、時間と計画が必要になります。 Microsoft Azure China 21Vianet に移行するには、次のようにします。
- 従来の Azure Service Manager (ASM) から Azure Resource Manager に[仮想マシンを移動します](/azure/china/china-how-to-rehost)。
- より適切かつ高速な移行を実現するために、[別のクラウド プロバイダーでホストされている既存のアプリケーションをリファクターします](/azure/china/china-how-to-refactor)。 
- [グローバル Azure でホストされている既存のアプリケーションを再ホストします](/azure/china/china-how-to-rehost)。
- 移行のリスクを軽減するために、テスト環境で [Global Connection Toolkit を使用して試験的な移行を実行します](https://github.com/Azure/AzureGlobalConnectionToolkit)。

## <a name="develop-for-azure-users"></a>Azure ユーザー向けに開発する
他のリージョンのユーザー向けにクラウド サービスを開発することに慣れている場合は、中国のユーザーが次のような期待を抱いていることを必ず考慮してください。
- **モバイル ファースト:** PC ではなく、モバイル機器がオンラインの世界の出発点と考えられています。 デザイン戦略は必ずモバイル中心としてください。
- **QR コードと画面スキャンの動作:** Web サイト、印刷広告、名刺、その他のメディアには通常、QR コードが含まれます。 訪問者が自分の携帯電話にサイトのモバイル版をすばやく読み込めるように、Web サイトのヘッダーとフッターに QR コードを含めてください。
- **コンテンツのローカライズ:** ローカライズは、コンテンツを翻訳するだけの作業ではありません。 ターゲット市場のデジタル環境とビジネス上の決定による文化的な影響を時間をかけて理解し、それに応じてコンテンツを調整してください。 

## <a name="use-social-sites-and-media-services-in-china"></a>中国のソーシャル サイトとメディア サービスを利用する
一般的に利用されている欧米のソーシャル メディア サイトおよびサービスは、中国ではブロックされる可能性があります。 中国で Web プレゼンスを管理する場合は、次の点に注意してください。
- Web サイトのフロントエンドで Google サービスに接続しないでください。 Google とそのすべてのサービスは中国でブロックされています。 最良の結果を得るには、サイトでの Google サービスの使用を避ける必要がある場合があります。 たとえば、Google マップを Baidu マップに置き換え、Google フォントの代わりにセルフホステッド フォントを使用します。
- YouTube や Vimeo からのビデオを埋め込まないでください。 中国では両方のサービスがブロックされています。 ビデオをローカルでホストするか、中国のビデオ ホスティング サイト (Youku、Qiyi、Tudou など) でホストするか、Azure Media Services を使用してください。 検索エンジンの最適化 (SEO) 監査ツールを使用して、中国で最も頻繁に使用される検索エンジンである Baidu 用にサイトを最適化してください。
- 中国特有のソーシャル ネットワークのプレゼンスを確立します。 Facebook、Twitter、Instagram などの世界的に人気のあるソーシャル ネットワークはブロックされています。 WeChat や Sina Weibo など、中国のソーシャル ネットワークに特化したソーシャル マーケティング戦略を策定してください。 Azure は現在、ローカル ソーシャル ネットワーク統合 (つまり、ソーシャル ID プロバイダー) を提供していません。

## <a name="check-endpoints-in-azure"></a>Azure のエンドポイントを確認する
Microsoft Azure China 21Vianet はグローバル Azure とは異なるため、サンプル コードや公開されているドキュメントなど、グローバル Azure ソースからの Azure サービス エンドポイントはすべて変更する必要があります。 

変更するエンドポイントを次の表に示します。 

関連項目:
- [中国の Azure アプリケーション開発者向けのメモ](https://msdn.microsoft.com/library/azure/dn578439.aspx)
- [中国の Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/confirmation.aspx?id=57062) 
- [開発者ガイド](https://www.azure.cn/documentation/articles/developerdifferences/#dev-guide) (中国語)

| サービス カテゴリ | グローバル Azure URI | Azure URI (中国) |
|-|-|-|
| Azure (一般) | \*.windows.net | \*.chinacloudapi.cn |
| Azure コンピューティング | \*.cloudapp.net | \*.chinacloudapp.cn |
| Azure Storage | \*.blob.core.windows.net \*.queue.core.windows.net \*.table.core.windows.net | \*.blob.core.chinacloudapi.cn \*.queue.core.chinacloudapi.cn \*.table.core.chinacloudapi.cn |
| Azure サービス管理 | https://management.core.windows.net | [https://management.core.chinacloudapi.cn](https://management.core.chinacloudapi.cn/) |
| Azure Resource Manager | [https://management.azure.com](https://management.azure.com/) | [https://management.chinacloudapi.cn](https://management.chinacloudapi.cn/) |
| Azure 管理ポータル | [https://portal.azure.com](https://portal.azure.com/) | [https://portal.azure.cn](https://portal.azure.cn/) |
| SQL Database | \*.database.windows.net | \*.database.chinacloudapi.cn |
| SQL Azure DB 管理 API | [https://management.database.windows.net](https://management.database.windows.net/) | [https://management.database.chinacloudapi.cn](https://management.database.chinacloudapi.cn/) |
| Azure Service Bus | \*.servicebus.windows.net | \*.servicebus.chinacloudapi.cn |
| Azure Access Control Service | \*.accesscontrol.windows.net | \*.accesscontrol.chinacloudapi.cn |
| Azure HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.cn |
| SQL DB インポート/エクスポート サービス エンドポイント | |  1.中国東部 [https://sh1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc](https://sh1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc) <br>2.中国北部 [https://bj1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc](https://bj1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc) |
| MySQL PaaS | | \*.mysqldb.chinacloudapi.cn |
| Azure Service Fabric クラスター | \*.cloudapp.azure.com | \*.chinaeast.chinacloudapp.cn |
| Azure Active Directory (AD) | \*.onmicrosoft.com | \*.partner.onmschina.cn |
| Azure AD ログオン | [https://login.microsoftonline.com](https://login.windows.net/) | [https://login.partner.microsoftonline.cn](https://login.chinacloudapi.cn/) |
| Azure AD Graph API | [https://graph.windows.net](https://graph.windows.net/) | [https://graph.chinacloudapi.cn](https://graph.chinacloudapi.cn/) |
| Microsoft Graph | [https://graph.microsoft.com](https://graph.microsoft.com/) | [https://microsoftgraph.chinacloudapi.cn](https://microsoftgraph.chinacloudapi.cn/) |
| Cognitive Services | <https://api.projectoxford.ai/face/v1.0> | <https://api.cognitive.azure.cn/face/v1.0> |
| Azure Key Vault API | \*.vault.azure.net | \*.vault.azure.cn |
| PowerShell によるログオン: <br>- 従来の Azure <br>- Azure Resource Manager <br>- Azure AD| - Add-AzureAccount<br>- Connect-AzureRmAccount <br> - Connect-msolservice |  - Add-AzureAccount -Environment AzureChinaCloud <br> - Connect-AzureRmAccount -Environment AzureChinaCloud <br>- Connect-msolservice -AzureEnvironment AzureChinaCloud |

## <a name="next-steps"></a>次の手順
- [開発者ガイド](https://www.azure.cn/documentation/articles/developerdifferences/#dev-guide) (中国語)
- [中国の Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/confirmation.aspx?id=57062)
- [パフォーマンスと接続性を管理する](/azure/china/china-how-to-manage-performance)
- [Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/)
