---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: msangapu
ms.openlocfilehash: bcbbd133c71b2917b73c200c4453ea57385fcaf1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532603"
---
| リソース | Free | 共有 | Basic | Standard | Premium (v1 から v3) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service プラン](../articles/app-service/overview-hosting-plans.md)あたりの [Web、モバイル、または API アプリ数](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |無制限<sup>2</sup> |無制限<sup>2</sup> |無制限<sup>2</sup> |無制限<sup>2</sup>|
| [App Service プラン](../articles/app-service/overview-hosting-plans.md) |リージョンあたり 10 件 |リソース グループあたり 10 |リソース グループあたり 100 |リソース グループあたり 100 |リソース グループあたり 100 |リソース グループあたり 100|
| コンピューティング インスタンスの種類 |共有 |共有 |専用<sup>3</sup> |専用<sup>3</sup> |専用<sup>3</sup></p> |専用<sup>3</sup>|
| [スケール アウト](../articles/app-service/manage-scale-up.md) (最大インスタンス) |1 Shared インスタンス |1 Shared インスタンス |3 専用インスタンス<sup>3</sup> |10 専用インスタンス<sup>3</sup> | 20 専用インスタンス (v1 および v2)、30 専用インスタンス (v3)。<sup>3</sup>|100 専用インスタンス<sup>4</sup>|
| ストレージ<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> 250 GB を超える場合は、サポート リクエストを送信してください。 |1 TB<sup>5</sup> <br/><br/> 使用可能なストレージ クォータは 999 GB です。 |
| CPU 時間 (5 分)<sup>6</sup> |3 分 |3 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金|
| CPU 時間 (1 日)<sup>6</sup> |約 60 分 |約 240 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |
| メモリ (1 時間) |App Service プランごとに 1,024 MB |アプリごとに 1,024 MB |該当なし |該当なし |該当なし |該当なし |
| 帯域幅 |165 MB |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |
| アプリケーションのアーキテクチャ |32 ビット |32 ビット |32 ビット/64 ビット |32 ビット/64 ビット |32 ビット/64 ビット |32 ビット/64 ビット |
| インスタンスごとの Web ソケット数<sup>7</sup> |5 |35 |350 |無制限 |無制限 |無制限 |
| インスタンスあたりの送信 IP 接続数 | 600 | 600 | インスタンス サイズによって異なる<sup>8</sup> | インスタンス サイズによって異なる<sup>8</sup> | インスタンス サイズによって異なる<sup>8</sup> | 16,000 |
| アプリケーションごとの同時 [デバッガー接続](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| サブスクリプションあたりの App Service 証明書数<sup>9</sup>| サポートされていません | サポートされていません |10 |10 |10 |10 |
| アプリケーションごとのカスタム ドメイン数</a> |0 (azurewebsites.net サブドメインのみ)|500 |500 |500 |500 |500 |
| カスタム ドメインの [SSL サポート](../articles/app-service/configure-ssl-certificate.md) |サポートされない、デフォルトで使用可能な \*.azurewebsites.net のワイルドカード証明書|サポートされない、デフォルトで使用可能な \*.azurewebsites.net のワイルドカード証明書|SNI SSL 接続は無制限 |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる | 無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる|
| Hybrid Connections (ハイブリッド接続) | | | プランあたり 5 | プランあたり 25 | アプリあたり 200 | アプリあたり 200 |
| [Virtual Network 統合](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [プライベート エンドポイント](../articles/app-service/networking/private-endpoint.md) | | |   |   |  アプリあたり 100  |    |
| 統合 Load Balancer | |X |X |X |X |X<sup>10</sup> |
| [アクセス制限](../articles/app-service/networking-features.md#access-restrictions) | アプリあたり 512 個のルール | アプリあたり 512 個のルール | アプリあたり 512 個のルール | アプリあたり 512 個のルール | アプリあたり 512 個のルール | アプリあたり 512 個のルール |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [スケジュールされたバックアップ](../articles/app-service/manage-backup.md) | | | | 2 時間ごとにスケジュールされたバックアップ。1 日あたり最大 12 回のバックアップ (手動 + スケジュール済み) | 1 時間ごとにスケジュールされたバックアップ。1 日あたり最大 50 回のバックアップ (手動 + スケジュール済み) | 1 時間ごとにスケジュールされたバックアップ。1 日あたり最大 50 回のバックアップ (手動 + スケジュール済み) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [エンドポイントの監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| アプリごとの[ステージング スロット](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| [運用環境でのテスト](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [診断ログ](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [認証と承認](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service マネージド証明書 (パブリック プレビュー)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/) <sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>アプリとストレージのクォータは、特に記述のない限り、App Service プラン単位の数字です。  
<sup>2</sup>これらのマシンで実際にホストできるアプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。  
<sup>3</sup>専用インスタンスのサイズはさまざまです。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。  
<sup>4</sup>要求に応じて追加できます。  
<sup>5</sup>ストレージの制限は、同じ App Service プランのすべてのアプリにまたがる合計コンテンツ サイズです。 1 つのリソース グループとリージョンを含めたすべての App Service プランのアプリのコンテンツサイズの合計は、500 GB を超えることはできません。  
<sup>6</sup>これらのリソースは、専用インスタンス上の物理リソース (インスタンスのサイズとインスタンス数) によって制限されます。  
<sup>7</sup>Basic レベルでアプリケーションを 2 つのインスタンスにスケーリングする場合、2 つのインスタンスのそれぞれに 350 本のコンカレント接続があります。 Standard レベル以上では、Web ソケットに理論的な制限はありませんが、他の要因によって Web ソケットの数が制限されることがあります。 たとえば、許可される最大同時要求数 (`maxConcurrentRequestsPerCpu` で定義される) は次のとおりです。小規模 VM あたり 7,500、中規模 VM あたり 15,000 (7,500 x 2 コア)、および大規模 VM あたり 75,000 (18,750 x 4 コア)。  
<sup>8</sup>最大 IP 接続数はインスタンス単位の数であり、インスタンスのサイズによって異なります。B1/S1/P1V3 インスタンスあたり 1,920、B2/S2/P2V3 インスタンスあたり 3,968、B3/S3/P3V3 インスタンスあたり 8,064。  
<sup>9</sup> サブスクリプションあたりの App Service 証明書のクォータ制限は、サポート要求によって最大上限 200 まで増やすことができます。  
<sup>10</sup>App Service Isolated SKU では、Azure Load Balancer を使用した内部負荷分散 (ILB) が可能です。そのため、インターネットからパブリック接続が発生しません。 そのため、ILB 分離 App Service の一部の機能は、ILB ネットワーク エンドポイントに直接アクセスできるマシンから使用する必要があります。  
<sup>11</sup> カスタムの実行可能ファイルやスクリプトを、オンデマンドやスケジュールで、または App Service インスタンス内のバックグラウンド タスクとして継続的に実行します。 Web ジョブを継続的に実行するには、常時接続が必要です。 App Service インスタンスで実行できる Web ジョブの数に定義済みの制限はありません。 実際にはアプリケーション コードの処理に依存する限界があります。

<sup>12</sup> ネイキッド ドメインはサポートされていません。 標準の証明書のみを発行します (ワイルドカード証明書は使用できません)。 カスタム ドメインあたり 1 つの無料証明書に制限されます。
