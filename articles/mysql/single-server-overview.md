---
title: 概要 - Azure Database for MySQL 単一サーバー
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービスである Azure Database for MySQL 単一サーバーについて説明します。
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: b0f8c5806ba22708db6dc537d391c1f1b1a183ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92543816"
---
# <a name="azure-database-for-mysql-single-server"></a>Azure Database for MySQL 単一サーバー

MySQL コミュニティ エディションを搭載した [Azure Database for MySQL](overview.md) は、次の 2 つのデプロイ モードで利用できます。
- シングル サーバー 
- フレキシブル サーバー (プレビュー)

この記事では、単一サーバー デプロイ モデルの概要を示し、主要概念について概説します。 フレキシブル サーバー デプロイ モードについては、[フレキシブル サーバーの概要](flexible-server/index.yml)を参照してください。 ワークロードに適したデプロイ オプションを決定する方法については、「[Azure で適切な MySQL サーバー オプションを選択する](select-right-deployment-type.md)」をご覧ください。

## <a name="overview"></a>概要

単一サーバーは、最小限の要件でデータベースをカスタマイズできる、完全に管理されたデータベースサービスです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、最小限のユーザー構成と制御によるセキュリティなど、データベース管理機能のほとんどを処理するよう設計されています。 このアーキテクチャは、1 つの可用性ゾーンで 99.99% の可用性を提供するように最適化されています。 MySQL 5.6、5.7、および 8.0 のコミュニティ バージョンがサポートされています。 このサービスは現時点で一般提供されており、さまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用できます。 

単一サーバーは、自動化されたパッチ適用を処理するように設計されていて、パッチ適用スケジュールや MySQL のカスタム構成設定をきめ細かに制御する必要がないクラウド ネイティブ アプリケーションに最適です。 

## <a name="high-availability"></a>高可用性

単一サーバー デプロイ モデルは、組み込みの高可用性と、低コストでの弾力性用に最適化されています。 このアーキテクチャでは、コンピューティングとストレージが分離されています。 データベース エンジンは独自のコンピューティング コンテナー上で実行され、データ ファイルは Azure Storage に格納されます。 ストレージには、データベース ファイルの 3 つのローカル冗長同期コピーが保持され、データの持続性が確保されます。 

計画済みまたは計画外のフェールオーバー イベント中にサーバーがダウンした場合、サービスでは次の自動化された手順を使用してサーバーの高可用性が維持されます。

1. 新しいコンピューティング コンテナーがプロビジョニングされます
2. データ ファイルを含むストレージが新しいコンテナーにマップされます 
3. 新しいコンピューティング コンテナーで MySQL データベース エンジンがオンラインになります
4. ゲートウェイ サービスによって透過的なフェールオーバーが確保されるので、アプリケーション側の変更は不要です。 
  
一般的なフェールオーバー時間は 60 秒から 120 秒です。 単一サーバー サービスのクラウド ネイティブの設計により、99.99% の可用性をサポートし、パッシブ ホット スタンバイのコストを削減することができます。

Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント (SLA) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Azure Database for MySQL 単一サーバー アーキテクチャの概念図"::: 

## <a name="automated-patching"></a>自動修正 

このサービスでは、基になるハードウェア、OS、およびデータベース エンジンの自動修正が実行されます。 パッチには、セキュリティとソフトウェアの更新プログラムが含まれています。 MySQL エンジンの場合、マイナー バージョンのアップグレードは自動的に行われ、パッチ サイクルの一部として含まれます。 パッチの適用に必要なユーザー アクションや構成設定はありません。 パッチの適用頻度は、ペイロードの重要度に基づいてサービスによって管理されます。 一般に、サービスは、継続的インテグレーションとリリースの一環として、毎月のリリース スケジュールに従います。 ユーザーは、[計画メンテナンスの通知](concepts-monitoring.md)にサブスクライブすることで、予定されているメンテナンスの通知をそのイベントの 72 時間前に受け取ることができます。

## <a name="automatic-backups"></a>自動バックアップ

単一サーバー サービスにより、サーバーのバックアップが自動的に作成され、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存されます。 バックアップを使用すると、サーバーを、バックアップのリテンション期間内の任意の時点に復元できます。 バックアップの既定のリテンション期間は 7 日です。 必要に応じて、リテンション期間を最大 35 日に構成できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。 詳細については、[バックアップ](concepts-backup.md)に関する記事をご覧ください。

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール

シングル サーバー サービスは、次の 3 つの SKU レベルで使用できます: Basic、汎用、メモリ最適化。 Basic レベルは、低コストの開発と低コンカレンシーのワークロードに最適です。 General Purpose および Memory Optimized は、高いコンカレンシー、スケール、予測可能なパフォーマンスを必要とする運用ワークロードに適しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 ストレージのスケーリングはオンラインであり、ストレージの自動拡張がサポートされています。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 消費したリソースについてだけ支払います。 詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="enterprise-grade-security-compliance-and-governance"></a>エンタープライズ グレードのセキュリティ、コンプライアンス、ガバナンス

単一サーバー サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 データ (バックアップを含む) と、クエリの実行中に作成される一時ファイルは暗号化されます。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステム マネージド (既定) または[カスタマー マネージド](concepts-data-encryption-mysql.md)にできます。 サービスでは、既定で適用されるトランスポート層セキュリティ (SSL/TLS) を使用して、動作中のデータが暗号化されます。 このサービスでは、TLS バージョン 1.2、1.1、1.0 がサポートされており、[TLS の最低バージョン](concepts-ssl-connection-security.md)を適用することができます。 

このサービスを使用すると、[プライベート リンク](concepts-data-access-security-private-link.md)を使用してサーバーにプライベート アクセスでき、[高度な脅威防止](concepts-data-access-and-security-threat-protection.md)の機能が提供されます。 高度な脅威防止では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出できます。

ネイティブ認証に加えて、単一サーバー サービスでは [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 認証がサポートされています。 Azure AD 認証は、Azure AD で定義および管理されている ID を使用して MySQL サーバーに接続するメカニズムです。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Azure サービスを一元管理でき、アクセスの制御が簡素化および一元化されます。

[監査ログ](concepts-audit-logs.md)を使用して、すべてのデータベース レベルのアクティビティを追跡できます。 

単一サーバー サービスは、FedRAMP、HIPAA、PCI DSS など、業界をリードするすべての認定に準拠しています。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/trustcenter/security)をご覧ください。 

Azure Database for MySQL のセキュリティ機能の詳細については、[セキュリティの概要](concepts-security.md)に関するページを参照してください。

## <a name="monitoring-and-alerting"></a>監視とアラート

単一サーバー サービスには、組み込みのパフォーマンス監視機能とアラート機能が搭載されています。 すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 このサービスでは遅いクエリのログを構成できます。また、このサービスには、他とは異なる[クエリ ストア](concepts-query-store.md)機能が用意されています。 クエリ ストアを使用すると、実行時間が最長のクエリおよびリソースを最も消費しているクエリを迅速に特定できるので、パフォーマンスのトラブルシューティングが簡単になります。 これらのツールを使用すると、ワークロードをすばやく最適化し、最適なパフォーマンスが得られるようにサーバーを構成することができます。 詳細については、[監視](concepts-monitoring.md)に関する記事を参照してください。

## <a name="migration"></a>移行

このサービスでは、MySQL のコミュニティ バージョンが実行されます。 これにより、アプリケーションの完全な互換性が確保され、MySQL エンジン上で開発された既存のアプリケーションを単一サーバー サービスに移行するために必要なリファクタリング コストが最小限に抑えられます。 単一サーバーへの移行は、次のいずれかのオプションを使用して実行できます。

- **ダンプと復元** – ユーザーがダウンタイムを許容できるオフライン移行では、mysqldump や mydumper などのコミュニティ ツールを使用してダンプと復元を行うことで、最も迅速に移行することができます。 詳細については、[ダンプと復元を使用した移行](concepts-migrate-dump-restore.md)に関する記事を参照してください。 
- **Azure Database Migration Service** – 最小限のダウンタイムで単一サーバーへのシームレスで簡素化された移行を行うには、[Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-online.md) を利用できます。 
- **データイン レプリケーション** – 移行のダウンタイムを最小限にするために、binlog ベースのレプリケーションに依存するデータイン レプリケーションを利用することもできます。 データイン レプリケーションは、移行をより細かく制御する必要がある現場のエキスパートが、最小限のダウンタイムで移行を行う場合に適しています。 詳細については、[データイン レプリケーション](concepts-data-in-replication.md)に関する記事をご覧ください。

## <a name="contacts"></a>連絡先
Azure Database for MySQL についての質問や提案は、Azure Database for MySQL チームにメール ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。

## <a name="next-steps"></a>次のステップ
Azure Database for MySQL 単一サーバー デプロイ モードの概要を確認したので、以下のことを行う準備ができました。

- 最初のサーバーを作成する。 
  - [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Azure CLI を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Azure Database for MySQL 用 Azure CLI サンプル](sample-scripts-azure-cli.md)

- 次の任意の言語を使って最初のアプリを作成します。
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
