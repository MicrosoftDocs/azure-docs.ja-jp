---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: c2db5740d6373c8f6be5dabe0260fe7f99575641
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554271"
---
| リソース | 無料 | 共有 | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service プラン](../articles/app-service/overview-hosting-plans.md)あたりの [Web、モバイル、または API アプリ数](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |無制限<sup>2</sup> |無制限<sup>2</sup> |無制限<sup>2</sup> |無制限<sup>2</sup>|
| [[App Service プラン]](../articles/app-service/overview-hosting-plans.md) |リージョンあたり 10 件 |リソース グループあたり 10 |リソース グループあたり 100 |リソース グループあたり 100 |リソース グループあたり 100 |リソース グループあたり 100|
| コンピューティング インスタンスの種類 |共有 |共有 |専用<sup>3</sup> |専用<sup>3</sup> |専用<sup>3</sup></p> |専用<sup>3</sup>|
| [スケール アウト](../articles/app-service/web-sites-scale.md) (最大インスタンス) |1 Shared インスタンス |1 Shared インスタンス |3 専用インスタンス<sup>3</sup> |10 専用インスタンス<sup>3</sup> |20 専用インスタンス<sup>3</sup>|100 専用インスタンス<sup>4</sup>|
| ストレージ<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU 時間 (5 分)<sup>6</sup> |3 分 |3 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金|
| CPU 時間 (1 日)<sup>6</sup> |約 60 分 |約 240 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |
| メモリ (1 時間) |App Service プランごとに 1,024 MB |アプリごとに 1,024 MB |該当なし |該当なし |該当なし |該当なし |
| 帯域幅 |165 MB |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |
| アプリケーションのアーキテクチャ |32 ビット |32 ビット |32 ビット/64 ビット |32 ビット/64 ビット |32 ビット/64 ビット |32 ビット/64 ビット |
| インスタンスごとの Web ソケット数<sup>7</sup> |5 |35 |350 |無制限 |無制限 |無制限 |
| アプリケーションごとの同時 [デバッガー接続](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| サブスクリプションあたりの App Service 証明書数<sup>10</sup>| サポートされていません | サポートされていません |10 |10 |10 |10 |
| アプリケーションごとのカスタム ドメイン数</a> |0 (azurewebsites.net サブドメインのみ)|500 |500 |500 |500 |500 |
| カスタム ドメインの [SSL サポート](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |サポートされない、デフォルトで使用可能な *.azurewebsites.net のワイルドカード証明書|サポートされない、デフォルトで使用可能な *.azurewebsites.net のワイルドカード証明書|SNI SSL 接続は無制限 |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる | 無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる|
| 統合 Load Balancer | |X |X |X |X |X<sup>9</sup> |
| [常時接続](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [スケジュールされたバックアップ](../articles/app-service/manage-backup.md) | | | | 2 時間ごとにスケジュールされたバックアップ。1 日あたり最大 12 回のバックアップ (手動 + スケジュール済み) | 1 時間ごとにスケジュールされたバックアップ。1 日あたり最大 50 回のバックアップ (手動 + スケジュール済み) | 1 時間ごとにスケジュールされたバックアップ。1 日あたり最大 50 回のバックアップ (手動 + スケジュール済み) |
| [Autoscale](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [Web ジョブ](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) のサポート | |X |X |X |X |X |
| [エンドポイントの監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [ステージング スロット](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.9% |99.95%|99.95%|99.95%|  

<sup>1</sup>アプリとストレージのクォータは、特に記述のない限り、App Service プラン単位の数字です。  
<sup>2</sup>これらのマシンで実際にホストできるアプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。  
<sup>3</sup>専用インスタンスのサイズはさまざまです。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。  
<sup>4</sup>要求に応じて追加できます。  
<sup>5</sup>ストレージの制限は、同じ App Service プランのすべてのアプリにまたがる合計コンテンツ サイズです。  
<sup>6</sup>これらのリソースは、専用インスタンス上の物理リソース (インスタンスのサイズとインスタンス数) によって制限されます。  
<sup>7</sup>Basic レベルでアプリケーションを 2 つのインスタンスにスケーリングする場合、2 つのインスタンスのそれぞれに 350 本のコンカレント接続があります。  
<sup>8</sup>カスタムの実行可能ファイルやスクリプトを、オンデマンドやスケジュールで、または App Service インスタンス内のバックグラウンド タスクとして継続的に実行します。 Web ジョブを継続的に実行するには、常時接続が必要です。 スケジュールされた Web ジョブの実行には、Azure Scheduler Free または Standard が必要です。 App Service インスタンスで実行できる Web ジョブの数に定義済みの制限はありません。 実際にはアプリケーション コードの処理に依存する限界があります。  
<sup>9</sup>App Service Isolated SKU では、Azure Load Balancer を使用した内部負荷分散 (ILB) が可能です。そのため、インターネットからパブリック接続が発生しません。 そのため、ILB 分離 App Service の一部の機能は、ILB ネットワーク エンドポイントに直接アクセスできるマシンから使用する必要があります。  
<sup>10</sup> サブスクリプションあたりの App Service Certificate のクォータ制限は、サポート要求によって最大上限 200 まで増やすことができます。  