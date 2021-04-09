---
title: Azure Cache for Redis とは
description: Azure Cache for Redis で、キャッシュ アサイド、コンテンツ キャッシング、ユーザー セッション キャッシイング、ジョブおよびメッセージ キュー、分散トランザクションなどを有効にする方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 5a0389b1074737728bd0ffa5d6db90d077a9f45f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652172"
---
# <a name="about-azure-cache-for-redis"></a>Azure Cache for Redis について
Azure Cache for Redis は、[Redis](https://redis.io/) ソフトウェアを基にしたインメモリ データ ストアを提供します。 Redis は、バックエンドでデータ ストアを酷使するアプリケーションのパフォーマンスとスケーラビリティを高めます。 高速に読み書きできるサーバーのメモリ内にアクセス頻度の高いデータを維持することで、大量のアプリケーション要求を処理することができます。 Redis によって、きわめて低遅延かつ高スループットのデータ ストレージ ソリューションがモダン アプリケーションにもたらされます。

Azure Cache for Redis は、Redis のオープン ソース (OSS Redis) と、マネージド サービスとしての Redis Labs (Redis Enterprise) の商用製品の両方を提供します。 安全かつ専用の Redis サーバー インスタンスを提供し、Redis API との完全な互換性を備えています。 このサービスは、Microsoft で管理されており、Azure 上でホストされ、Azure 内外の任意のアプリケーションからアクセスできます。

Azure Cache for Redis は、分散データ (またはコンテンツ) キャッシュ、セッション ストア、メッセージ ブローカーなどとして使用できます。 スタンドアロンとしてデプロイできるほか、Azure SQL や Cosmos DB など、他の Azure データベース サービスと共にデプロイすることができます。

## <a name="key-scenarios"></a>主なシナリオ
Azure Cache for Redis では、一般的なアプリケーションのアーキテクチャ パターンがサポートされているため、アプリケーションのパフォーマンスが向上します。 次に、最も一般的なものをいくつか示します。

| Pattern      | 説明                                        |
| ------------ | -------------------------------------------------- |
| [データ キャッシュ](cache-web-app-cache-aside-leaderboard.md) | 多くの場合、データベースが大きすぎてキャッシュに直接読み込むことができません。 [キャッシュ アサイド](/azure/architecture/patterns/cache-aside) パターンを使用して、必要な場合にだけデータをキャッシュに読み込むのが一般的です。 システムがデータを変更した場合、システムはキャッシュも更新できます。それが後で他のクライアントに配布されます。 さらに、システムでは、データの有効期限を設定したり、削除ポリシーを使用してキャッシュへのデータの更新をトリガーしたりすることができます。|
| [コンテンツ キャッシュ](cache-aspnet-output-cache-provider.md) | 多くの Web ページは、ヘッダー、フッター、バナーなどの静的コンテンツを使用するテンプレートから生成されます。 これらの静的項目は、頻繁には変更されません。 メモリ内キャッシュを使用すると、バックエンド データストアと比較して、静的コンテンツに迅速にアクセスできます。 このパターンでは、処理時間とサーバーの負荷が軽減されるため、Web サーバーの応答性が向上します。 負荷に対処するために必要なサーバー数を減らすことができます。 Azure Cache for Redis は、このパターンを ASP.NET でサポートするために、Redis 出力キャッシュ プロバイダーを提供します。|
| [セッション ストア](cache-aspnet-session-state-provider.md) | このパターンは通常、Web アプリケーションでユーザーの Cookie に関連付けられる、ショッピング カートなどのユーザー履歴データに使用されます。 Cookie に格納する情報量が多すぎると、Cookie のサイズが大きくなり、大きなサイズ のCookie が要求ごとに渡されたり検証されたりするため、パフォーマンスに悪影響を及ぼす場合があります。 一般的なソリューションでは、データベース内のデータにクエリを実行するためのキーとして、Cookie を使用します。 ユーザーに情報を関連付けるために、Azure Cache for Redis などのメモリ内キャッシュを使用すると、リレーショナル データベース全体を操作するよりも、はるかに高速になります。 |
| ジョブおよびメッセージ キュー | アプリケーションは、多くの場合、要求に関連付けられている操作の実行に時間がかかると、キューにタスクを追加します。 実行時間の長い操作は、通常は別のサーバーによって順番に処理されるように、キューに登録されます。  作業を延期するこの手法は、タスク キューと呼ばれます。 Azure Cache for Redis では、アプリケーションでこのパターンを有効にするために、分散キューを提供します。|
| 分散トランザクション | アプリケーションは、バックエンド データストアに対する一連のコマンドを単一のアトミック操作として実行することを必要とする場合があります。 すべてのコマンドが成功する必要があり、成功しなかった場合はすべて、初期の状態にロールバックされる必要があります。 Azure Cache for Redis では、一連のコマンドを単一の[トランザクション](https://redis.io/topics/transactions)として実行する方法をサポートしています。 |

## <a name="redis-versions"></a>Redis のバージョン

Azure Cache for Redis では、OSS Redis バージョン 4.x および 6.0 (プレビュー) をサポートしています。 Redis 5.0 をスキップして最新バージョンをご提供することが決定しました。 これまで、Azure Cache for Redis は、1 つの Redis バージョンにのみ対応していました。 今後は、新しいメジャー リリース アップグレードと、少なくとも 1 つ前の安定バージョンが提供される予定です。 アプリケーションにとって最適な[バージョンを選択する](cache-how-to-version.md)ことができます。


## <a name="service-tiers"></a>サービス階層
Azure Cache for Redis は以下のレベルでご利用いただけます。

| レベル | 説明 |
|---|---|
| Basic | 1 つの VM 上で実行される OSS Redis キャッシュ。 このレベルには、サービス レベル アグリーメント (SLA) はありません。開発やテスト用のワークロード、およびクリティカルではないワークロードに適しています。 |
| Standard | レプリケートされた構成において、2 つの VM 上で実行される OSS Redis キャッシュ。 |
| Premium | 高パフォーマンスの OSS Redis キャッシュ。 このレベルでは、スループットの向上、待ち時間の短縮、可用性の強化などの機能が提供されます。 Premium キャッシュは、Basic または Standard のキャッシュと比較して、より強力な VM にデプロイされます。 |
| エンタープライズ | Redis Labs の Redis Enterprise ソフトウェアを搭載した高パフォーマンスのキャッシュ。 このレベルは、RediSearch、RedisBloom、RedisTimeSeries などの Redis モジュールをサポートします。 さらに、Premium レベルよりも高い可用性を提供します。 |
| Enterprise Flash | Redis Labs の Redis Enterprise ソフトウェアを搭載したコスト効果の高い大規模キャッシュ。 このレベルでは、Redis データ ストレージが、VM 上の、DRAM より低コストの非揮発性メモリに拡張されます。 これにより、GB あたりのメモリ コストが全体的に削減されます。 |

### <a name="feature-comparison"></a>機能の比較
「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」に、各レベルの詳細な比較が示されています。 次の表に、レベル別にサポートされる一部の機能を紹介します。

| 機能の説明 | Basic | Standard | Premium | エンタープライズ | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| データの暗号化 |✔|✔|✔|✔|✔|
| [ネットワークの分離](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [スケーリング](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [OSS クラスター](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [データの永続化](cache-how-to-premium-persistence.md) |-|-|✔|プレビュー|プレビュー|
| [ゾーン冗長性](cache-how-to-zone-redundancy.md) |-|-|プレビュー|✔|✔|
| [geo レプリケーション](cache-how-to-geo-replication.md) |-|-|✔|プレビュー|プレビュー|
| [モジュール](https://redis.io/modules) |-|-|-|✔|✔|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [スケジュールされた更新](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>最適なサービス レベルを選択する
Azure Cache for Redis のレベルを選ぶ際は、次の点を考慮する必要があります。

* **メモリ**:Basic および Standard レベルでは 250 MB から 53 GB、Premium レベルでは 6 GB から 1.2 TB、Enterprise レベルでは 12 GB から 14 TB が提供されます。  120 GB を超える Premium レベルのキャッシュを作成する場合は、Redis OSS クラスタリングを使用できます。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。 詳細については、「[How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md)」 (Premium Azure Cache for Redis のクラスタリングの構成方法) を参照してください。
* **パフォーマンス**: Premium および Enterprise レベルのキャッシュは、高速プロセッサを備えたハードウェアにデプロイされ、Basic レベルや Standard レベルと比べて優れたパフォーマンスを発揮します。 Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。 詳細については、「[Azure Cache for Redis のパフォーマンス](cache-planning-faq.md#azure-cache-for-redis-performance)」を参照してください。
* **Redis サーバー専用コア**: C0 を除くすべてのキャッシュは、専用の VM コアを実行します。 Redis は設計上、コマンドの処理に使用されるスレッドが 1 つだけです。 Azure Cache for Redis では、I/O 処理に追加のコアが利用されます。 使用コア数が増えることで、線形スケーリングは得られないまでも、スループット パフォーマンスは向上します。 加えて、VM サイズが大きくなれば、それよりも小さい VM と比べて、帯域幅の制限も引き上げられるのが一般的です。 これにより、アプリケーションのタイムアウトを引き起こすネットワークの飽和状態を避けることができます。
* **ネットワーク パフォーマンス**: 高いスループットを必要とするワークロードがある場合、Premium および Enterprise レベルでは、Basic や Standard と比較してより広い帯域幅が提供されます。 また、各レベル内では、キャッシュをホストする基盤の VM のため、キャッシュのサイズが大きいほど帯域幅も増えます。 詳細については、「[Azure Cache for Redis のパフォーマンス](cache-planning-faq.md#azure-cache-for-redis-performance)」を参照してください。
* **最大クライアント接続数**:Premium レベルでは、Redis に接続できる最大数のクライアントが提供されます。キャッシュのサイズが大きいほど、接続の数は多くなります。 クラスタリングでは、クラスター化されたキャッシュで使用できる接続の数は増加しません。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。
* **高可用性**: Azure Cache for Redis には、複数の [高可用性](cache-high-availability.md)オプションが用意されています。 Standard、Premium、または Enterprise キャッシュの可用性は [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) に従って提供されることが保証されています。 SLA は、キャッシュ エンドポイントへの接続のみをカバーします。 SLA は、データ損失からの保護には対応していません。 Premium レベルと Enterprise レベルの Redis データの永続化機能を使用して、データ損失に対する復元性を高めることをお勧めします。
* **データの永続化**: Premium および Enterprise レベルではそれぞれ、Azure Storage アカウントとマネージド ディスクにキャッシュデータを永続化できます。 基になるインフラストラクチャで問題が発生すると、データが失われる可能性があります。 Redis データの永続化機能を使用して、データ損失に対する復元性を高めることをお勧めします。 Azure Cache for Redis では、RDB オプションと AOF オプション (プレビュー) の両方が用意されています。 データの永続化は、Azure portal と CLI を使用して有効にすることができます。 Premium レベルについては、[Premium Azure Cache for Redis の永続化の構成方法](cache-how-to-premium-persistence.md)に関するページを参照してください。
* **ネットワークの分離**: Azure Private Link と Virtual Network (VNET) のデプロイにより、Azure Cache for Redis のセキュリティとトラフィックの分離が強化されます。 VNET を使用すると、ネットワーク アクセス制御ポリシーを介してアクセスをさらに制限することができます。 詳細については、[Azure Private Link を使用した Azure Cache for Redis](cache-private-link.md) に関するページおよび「[Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」を参照してください。
* **拡張性**: Enterprise レベルでは、[RediSearch](https://docs.redislabs.com/latest/modules/redisearch/)、[RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/)、[RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/) がサポートされます。 これらのモジュールにより、Redis に新しいデータ型と機能が追加されます。

作成後のキャッシュは、Basic レベルから Premium までスケーリングできます。 より低いレベルへのスケールダウンは、現在サポートされていません。 スケーリングの詳細な手順については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」(Azure Redis Cache のスケーリング方法) と「[How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」(スケーリング処理を自動化する方法) を参照してください。

### <a name="special-considerations-for-enterprise-tiers"></a>Enterprise レベルの特別な考慮事項

Enterprise レベルは Redis Enterprise (Redis Labs の Redis 商用版) に依存しています。 お客様は、Azure Marketplace プランを通じて本ソフトウェアのライセンスを取得し、お支払いいただきます。 Azure Cache for Redis はライセンス取得が容易で、別途これを行う必要はありません。 Azure Marketplace で購入するには、以下の前提条件が整っている必要があります。
* お使いの Azure サブスクリプションに、有効な支払い方法が備わっている。 Azure クレジットや無料の MSDN サブスクリプションはサポートされていません。
* 組織が [Azure Marketplace での購入](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)を許可している。
* プライベート マーケットプレースを使用する場合は、そこに Redis Labs Enterprise プランが含まれている必要がある。

> [!IMPORTANT]
> Azure Cache for Redis Enterprise キャッシュには、キャッシュ インスタンス自体とは別に課金される標準のネットワーク ロード バランサーが必要です。 詳細については、[Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)に関するページをご覧ください。 Enterprise キャッシュが複数の Availability Zones で構成されている場合、データ転送は 2021 年 7 月 1 日からの[標準のネットワーク帯域幅料金](https://azure.microsoft.com/pricing/details/bandwidth/)で課金されます。
>
> さらに、データ永続化によって Managed Disks が追加されます。 これらのリソースの使用は、Enterprise データ永続化のパブリック プレビュー期間中は無料になります。 これは、機能が一般公開されると変更される可能性があります。
>
>

## <a name="next-steps"></a>次のステップ
* [オープンソースの Redis キャッシュを作成する](quickstart-create-redis.md)
* [Redis Enterprise キャッシュを作成する](quickstart-create-redis-enterprise.md)
* [ASP.NET Web アプリで Azure Cache for Redis を使用する](cache-web-app-howto.md)
* [.NET Core で Azure Cache for Redis を使用する](cache-dotnet-core-quickstart.md)
* [.NET Framework で Azure Cache for Redis を使用する](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Node.js で Azure Cache for Redis を使用する](cache-nodejs-get-started.md)
* [Java で Azure Cache for Redis を使用する](cache-java-get-started.md)
* [Python で Azure Cache for Redis を使用する](cache-python-get-started.md)