---
title: Azure Cache for Redis とは | Microsoft Docs
description: Azure Cache for Redis の概要と一般的な使用方法について説明します。
services: cache
documentationcenter: ''
author: yegu-ms
manager: martinekuan
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 1f0c943bed473178dadb09cfb9d355821e5236e8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066842"
---
# <a name="azure-cache-for-redis-description"></a>Azure Cache for Redis の説明

Azure Cache for Redis は、人気のあるソフトウェア [Redis](https://redis.io/) が基になっています。 Azure Redis Cache は通常、バックエンドのデータストアに大きく依存するシステムのパフォーマンスとスケーラビリティを向上させるためのキャッシュとして使用されます。 アプリケーションに近い場所にある高速ストレージに、アクセス頻度が高いデータを一時的にコピーすることで、パフォーマンスが向上します。 [Azure Cache for Redis](https://redis.io/) を使用すると、この高速ストレージは、データベースによってディスクから読み込まれずに、Azure Cache for Redis を使用してメモリ内に配置されます。

Azure Cache for Redis は、メモリ内のデータ構造ストア、分散型の非リレーショナル データベース、およびメッセージ ブローカーとして使用することもできます。 Redis エンジンの短い待機時間、高スループットのパフォーマンスを活用して、アプリケーションのパフォーマンスが向上します。

Azure Cache for Redis を使用すると、セキュリティ保護された専用の Redis Cache にアクセスできます。 Azure Cache for Redis は、Microsoft で管理されており、Azure 内でホストされ、Azure 内外の任意のアプリケーションからアクセスできます。

## <a name="using-azure-cache-for-redis"></a>Azure Cache for Redis の使用

アプリケーションのアーキテクチャをサポートするため、またはアプリケーションのパフォーマンスを向上させるために、Azure Cache for Redis が使用される多数の一般的なパターンがあります。 次に、最も一般的なものをいくつか示します。

| Pattern      | 説明                                        |
| ------------ | -------------------------------------------------- |
| [キャッシュ アサイド](cache-web-app-cache-aside-leaderboard.md) | データベースが大規模になる場合があるため、データベース全体をキャッシュに読み込む方法は、推奨されません。 [キャッシュ アサイド](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) パターンを使用して、必要な場合にだけデータ項目をキャッシュに読み込むのが一般的です。 システムでバックエンド データへの変更が行われた場合、その時点で、他のクライアントと共に配布されるキャッシュも更新できます。 さらに、システムでは、データ項目の有効期限を設定したり、削除ポリシーを使用してデータを更新し、キャッシュへの再読み込みを行ったりすることが可能です。|
| [コンテンツ キャッシュ](cache-aspnet-output-cache-provider.md) | ほとんどの Web ページは、ヘッダー、フッター、ツールバー、メニューなどを含むテンプレートから生成されます。テンプレートは、実際には頻繁に変更されることはなく、動的に生成されるものではありません。 Azure Cache for Redis などのメモリ内キャッシュを使用すると、バックエンド データストアと比較して、Web サーバーからこの種類の静的コンテンツに迅速にアクセスできます。 このパターンでは、コンテンツを動的に生成するために必要な処理時間とサーバー負荷を軽減します。 これにより、Web サーバーの応答性が高まり、負荷に対処するために必要なサーバー数を減らすことができます。 Azure Cache for Redis は、このパターンをサポートするために役立つ Redis 出力キャッシュ プロバイダーを ASP.NET に提供します。|
| [ユーザー セッション キャッシュ](cache-aspnet-session-state-provider.md) | このパターンは通常、Web アプリケーションでユーザーの Cookie に関連付けられるショッピング カートなどのユーザー履歴の種類情報に使用されます。 Cookie に格納する情報量が多すぎると、Cookie のサイズが大きくなり、大きなサイズ のCookie が要求ごとに渡されたり検証されたりするため、パフォーマンスに悪影響を及ぼす場合があります。 一般的なソリューションとしては、バックエンド データベース内のデータにクエリを実行するためのキーとして、Cookie を使用します。 ユーザーに情報を関連付けるために、Azure Cache for Redis などのメモリ内キャッシュを使用すると、リレーショナル データベース全体を操作するよりも、はるかに高速になります。 |
| ジョブおよびメッセージ キュー | アプリケーションが要求を受け取ると、多くの場合、要求に関連付けられている処理を実行するために、追加の時間がかかります。 キューに追加することで実行時間の長い操作を延期するのが、一般的なパターンです。このような要求は、場合によっては他のサーバーで、後から処理します。 作業を延期するこの手法は、タスク キューと呼ばれます。 タスク キューをサポートするために設計された多数のソフトウェア コンポーネントがあります。 また、Azure Cache for Redis では、この目的のために、分散キューも提供しています。|
| 分散トランザクション | アプリケーションが、バックエンド データストアに対する一連のコマンドを単一の操作 (アトミック) として実行できることが、一般的な要件です。 すべてのコマンドが成功する必要があり、成功しなかった場合はすべて、初期の状態にロールバックされる必要があります。 Azure Cache for Redis では、一連のコマンドを[トランザクション](https://redis.io/topics/transactions)の形式で単一の操作として実行する方法をサポートしています。 |

## <a name="azure-cache-for-redis-offerings"></a>Azure Cache for Redis のプラン

Azure Cache for Redis は以下のレベルでご利用いただけます。

| レベル | 説明 |
|---|---|
Basic | 単一ノード キャッシュ。 このレベルでは、複数のメモリ サイズ (250 MB ～ 53 GB) をサポートします。 これは、開発/テスト、およびクリティカルではない負荷に最適なレベルです。 Basic レベルには、サービス レベル アグリーメント (SLA) はありません。 |
| Standard | Microsoft が管理しているプライマリとセカンダリの 2 つのノード構成にレプリケートされたキャッシュ。高可用性の SLA が付きます (99.9%)。 |
| Premium | Premium レベルは、エンタープライズ対応レベルです。 Premium レベルのキャッシュでは、より多くの機能をサポートし、より短い待機時間でより高いスループットを実現します。 Premium レベルのキャッシュは、Basic または Standard レベルと比較して優れたパフォーマンスを発揮する、より高性能なハードウェア上にデプロイされます。 この利点によって、Premium レベルでは、Standard レベルと比較して、同じサイズのキャッシュのスループットがより高くなります。 |

> [!TIP]
> Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」(Azure Redis Cache の FAQ) を参照してください。
>

キャッシュは、既に作成された後に、より高いレベルにスケールアップできます。 より低いレベルへのスケールダウンは、サポートされていません。 スケーリングの詳細な手順については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」(Azure Redis Cache のスケーリング方法) と「[How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」(スケーリング処理を自動化する方法) を参照してください。

### <a name="feature-comparison"></a>機能の比較

「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」ページに、各レベルの詳細な比較が示されています。 次の表に、レベル別にサポートされる一部の機能を紹介します。

| 機能の説明 | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis データの保持](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis クラスター](cache-how-to-premium-clustering.md) |✔|-|-|
| [ファイアウォール規則によるセキュリティ](cache-configure.md#firewall) |✔|✔|✔|
| 転送中の暗号化 |✔|✔|✔|
| [VNet による強化されたセキュリティと分離](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [スケジュールされた更新](cache-administration.md#schedule-updates) |✔|✔|✔|
| [geo レプリケーション](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>次の手順

* [ASP.NET Web アプリ クイック スタート](cache-web-app-howto.md): Azure Cache for Redis を使用する単純な ASP.NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-how-to-use-azure-redis-cache.md): Azure Cache for Redis を使用する .NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-core-quickstart.md): Azure Cache for Redis を使用する .NET Core アプリを作成します。
* [Node.js クイック スタート](cache-nodejs-get-started.md): Azure Redis Cache を使用する単純な Node.js アプリを作成します。
* [Java クイック スタート](cache-java-get-started.md): Azure Cache for Redis を使用する単純な Java アプリを作成します。
* [Python クイック スタート](cache-python-get-started.md): Azure Cache for Redis を使用する Python アプリを作成します。
