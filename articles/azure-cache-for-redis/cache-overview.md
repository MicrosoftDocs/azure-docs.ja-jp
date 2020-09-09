---
title: Azure Cache for Redis とは
description: Azure Cache for Redis で、キャッシュ アサイド、コンテンツ キャッシング、ユーザー セッション キャッシイング、ジョブおよびメッセージ キュー、分散トランザクションなどを有効にする方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 3751560125ea8ac6cc00ed63521bff30b751e688
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009598"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis は、オープンソース ソフトウェア [Redis](https://redis.io/) を基にしたインメモリ データ ストアを提供します。 Redis は、バックエンドでデータ ストアを酷使するアプリケーションのパフォーマンスとスケーラビリティを高めます。 高速に読み書きできるサーバーのメモリ内にアクセス頻度の高いデータを維持することで、大量のアプリケーション要求を処理することができます。 Redis によって、きわめて低遅延かつ高スループットのデータ ストレージ ソリューションがモダン アプリケーションにもたらされます。

Azure Cache for Redis は、Redis をマネージド サービスとして提供するものです。 安全かつ専用の Redis サーバー インスタンスを提供し、Redis API との完全な互換性を備えています。 このサービスは、Microsoft で管理されており、Azure 上でホストされ、Azure 内外の任意のアプリケーションからアクセスできます。

Azure Cache for Redis は、分散データ (またはコンテンツ) キャッシュ、セッション ストア、メッセージ ブローカーなどとして使用できます。 スタンドアロンとしてデプロイできるほか、Azure SQL や Cosmos DB など、他の Azure データベース サービスと共にデプロイすることができます。

## <a name="key-scenarios"></a>主なシナリオ
Azure Cache for Redis では、一般的なアプリケーションのアーキテクチャ パターンがサポートされているため、アプリケーションのパフォーマンスが向上します。 次に、最も一般的なものをいくつか示します。

| Pattern      | 説明                                        |
| ------------ | -------------------------------------------------- |
| [データ キャッシュ](cache-web-app-cache-aside-leaderboard.md) | 多くの場合、データベースが大きすぎてキャッシュに直接読み込むことができません。 [キャッシュ アサイド](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) パターンを使用して、必要な場合にだけデータをキャッシュに読み込むのが一般的です。 システムがデータを変更した場合、システムはキャッシュも更新できます。それが後で他のクライアントに配布されます。 さらに、システムでは、データの有効期限を設定したり、削除ポリシーを使用してキャッシュへのデータの更新をトリガーしたりすることができます。|
| [コンテンツ キャッシュ](cache-aspnet-output-cache-provider.md) | 多くの Web ページは、ヘッダー、フッター、バナーなどの静的コンテンツを使用するテンプレートから生成されます。 これらの静的項目は、頻繁には変更されません。 メモリ内キャッシュを使用すると、バックエンド データストアと比較して、静的コンテンツに迅速にアクセスできます。 このパターンでは、処理時間とサーバーの負荷が軽減されるため、Web サーバーの応答性が向上します。 負荷に対処するために必要なサーバー数を減らすことができます。 Azure Cache for Redis は、このパターンを ASP.NET でサポートするために、Redis 出力キャッシュ プロバイダーを提供します。|
| [セッション ストア](cache-aspnet-session-state-provider.md) | このパターンは通常、Web アプリケーションでユーザーの Cookie に関連付けられる、ショッピング カートなどのユーザー履歴データに使用されます。 Cookie に格納する情報量が多すぎると、Cookie のサイズが大きくなり、大きなサイズ のCookie が要求ごとに渡されたり検証されたりするため、パフォーマンスに悪影響を及ぼす場合があります。 一般的なソリューションでは、データベース内のデータにクエリを実行するためのキーとして、Cookie を使用します。 ユーザーに情報を関連付けるために、Azure Cache for Redis などのメモリ内キャッシュを使用すると、リレーショナル データベース全体を操作するよりも、はるかに高速になります。 |
| ジョブおよびメッセージ キュー | アプリケーションは、多くの場合、要求に関連付けられている操作の実行に時間がかかると、キューにタスクを追加します。 実行時間の長い操作は、通常は別のサーバーによって順番に処理されるように、キューに登録されます。  作業を延期するこの手法は、タスク キューと呼ばれます。 Azure Cache for Redis では、アプリケーションでこのパターンを有効にするために、分散キューを提供します。|
| 分散トランザクション | アプリケーションは、バックエンド データストアに対する一連のコマンドを単一のアトミック操作として実行することを必要とする場合があります。 すべてのコマンドが成功する必要があり、成功しなかった場合はすべて、初期の状態にロールバックされる必要があります。 Azure Cache for Redis では、一連のコマンドを単一の[トランザクション](https://redis.io/topics/transactions)として実行する方法をサポートしています。 |

## <a name="service-tiers"></a>サービス階層
Azure Cache for Redis は以下のレベルでご利用いただけます。

| レベル | 説明 |
|---|---|
Basic | 単一ノード キャッシュ。 このレベルでは、複数のメモリ サイズ (250 MB から 53 GB) がサポートされており、開発、テスト、および重要ではないワークロードに最適です。 Basic レベルには、サービスレベル アグリーメント (SLA) はありません。 |
| Standard | Azure によって構成が管理されているプライマリとレプリカの 2 つのノードにレプリケートされたキャッシュ。高可用性の [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) が付きます。 |
| Premium | Premium レベルは、エンタープライズ対応レベルです。 Premium レベルのキャッシュでは、より多くの機能をサポートし、より短い待機時間でより高いスループットを実現します。 Premium レベルのキャッシュは、Basic または Standard レベルと比較して優れたパフォーマンスを発揮する、より高性能なハードウェア上にデプロイされます。 この利点によって、Premium レベルでは、Standard レベルと比較して、同じサイズのキャッシュのスループットがより高くなります。 |

### <a name="feature-comparison"></a>機能の比較
「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」に、各レベルの詳細な比較が示されています。 次の表に、レベル別にサポートされる一部の機能を紹介します。

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

### <a name="choosing-the-right-tier"></a>最適なサービス レベルを選択する
Azure Cache for Redis のレベルを選ぶ際は、次の点を考慮する必要があります。

* **メモリ**:Basic レベルと Standard レベルでは、250 MB ～ 53 GB です。 Premium サービス レベルでは、最大 1.2 TB (クラスター) または 120 GB (非クラスター) が提供されます。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。
* **ネットワーク パフォーマンス**: 高いスループットを必要とするワークロードがある場合、Premium レベルでは、Standard や Basic と比較してより広い帯域幅が提供されます。 また、各レベル内では、キャッシュをホストする基盤の VM のため、キャッシュのサイズが大きいほど帯域幅も増えます。 詳細については、「[Azure Cache for Redis のパフォーマンス](cache-planning-faq.md#azure-cache-for-redis-performance)」を参照してください。
* **スループット**:Premium レベルでは、提供されているうちで最大のスループットが提供されます。 キャッシュ サーバーまたはクライアントが帯域幅の限界に達した場合、クライアント側でタイムアウトが発生する場合があります。 詳細については、後の表を参照してください。
* **高可用性**: Azure Cache for Redis では、Standard と Premium キャッシュの可用性が [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) に準拠することが保証されています。 SLA は、キャッシュ エンドポイントへの接続のみをカバーします。 SLA は、データ損失からの保護には対応していません。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。
* **Redis データの永続化**: Premium レベルでは、Azure Storage アカウント内のキャッシュ データを永続化できます。 Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。 基になるインフラストラクチャで問題が発生すると、データが失われる可能性があります。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。 Azure Cache for Redis では、Redis 永続化の RDB オプションと AOF オプション (プレビュー) が用意されています。 詳細については、[Premium Azure Cache for Redis の永続化の構成方法](cache-how-to-premium-persistence.md)についてのページを参照してください。
* **Redis クラスター**: Premium サービス レベルで利用可能な Redis クラスタリングを使用すると、120 GB を超えるキャッシュを作成したり、複数の Redis ノード間でデータをシャード化したりできます。 各ノードは、高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。 詳細については、「[How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md)」 (Premium Azure Cache for Redis のクラスタリングの構成方法) を参照してください。
* **セキュリティとネットワークの分離の強化**:Azure Virtual Network (VNET) のデプロイにより、Azure Cache for Redis のセキュリティと分離が強化されると共に、サブネット、アクセス制御ポリシー、アクセスをさらに制限する他の機能も提供されます。 詳細については、[Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)に関するページを参照してください。
* **Redis の構成**:Standard レベルと Premium レベルのどちらでも、キースペース通知のために Redis を構成できます。
* **最大クライアント接続数**:Premium レベルでは、Redis に接続できる最大数のクライアントが提供されます。キャッシュのサイズが大きいほど、接続の数は多くなります。 クラスタリングでは、クラスター化されたキャッシュで使用できる接続の数は増加しません。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。
* **Redis サーバー専用コア**: Premium レベルでは、すべてのキャッシュ サイズに Redis 専用のコアがあります。 Basic/Standard レベルでは、C1 サイズ以上に Redis サーバー専用コアがあります。
* **シングルスレッド処理**: Redis は設計上、コマンドの処理に使用されるスレッドが 1 つだけです。 Azure Cache for Redis では、I/O 処理に追加のコアも利用されます。 使用コア数が増えることで、線形スケーリングは得られないまでも、スループット パフォーマンスは向上します。 加えて、VM サイズが大きくなれば、それよりも小さい VM と比べて、帯域幅の制限も引き上げられるのが一般的です。 これにより、アプリケーションのタイムアウトを引き起こすネットワークの飽和状態を避けることができます。
* **パフォーマンスの向上**:Premium レベルのキャッシュは、高速プロセッサを備えたハードウェアにデプロイされ、Basic レベルや Standard レベルと比べて優れたパフォーマンスを発揮します。 Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。 詳細については、「[Azure Cache for Redis のパフォーマンス](cache-planning-faq.md#azure-cache-for-redis-performance)」を参照してください。

キャッシュは、作成後に、より高いレベルにスケールアップできます。 より低いレベルへのスケールダウンは、サポートされていません。 スケーリングの詳細な手順については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」(Azure Redis Cache のスケーリング方法) と「[How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」(スケーリング処理を自動化する方法) を参照してください。

## <a name="next-steps"></a>次のステップ
* [ASP.NET Web アプリ クイック スタート](cache-web-app-howto.md): Azure Cache for Redis を使用する単純な ASP.NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-how-to-use-azure-redis-cache.md): Azure Cache for Redis を使用する .NET Web アプリを作成します。
* [.NET クイック スタート](cache-dotnet-core-quickstart.md): Azure Cache for Redis を使用する .NET Core アプリを作成します。
* [Node.js クイック スタート](cache-nodejs-get-started.md): Azure Redis Cache を使用する単純な Node.js アプリを作成します。
* [Java クイック スタート](cache-java-get-started.md): Azure Cache for Redis を使用する単純な Java アプリを作成します。
* [Python クイック スタート](cache-python-get-started.md): Azure Cache for Redis を使用する Python アプリを作成します。
