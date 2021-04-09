---
title: Azure Migrate のよくあるご質問
description: Azure Migrate サービスに関する一般的な質問の回答を示します。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377382"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。 この記事を読んでから疑問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿できます。 また、次の記事も参照できます。

- [Azure Migrate アプライアンス](common-questions-appliance.md)に関する質問
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md)に関する質問

## <a name="what-is-azure-migrate"></a>Azure Migrate とは

Azure Migrate は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの ISV オファリングが用意されています。 [詳細については、こちらを参照してください](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate でできること

Azure Migrate を使用して、オンプレミスのインフラストラクチャ、アプリケーション、データの検出および評価を行い、Azure に移行します。 Azure Migrate は、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想化された VM、データベース、Web アプリ、仮想デスクトップの評価と移行をサポートしています。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate と Azure Site Recovery の違い

[Azure Migrate](migrate-services-overview.md) は、評価と Azure への移行を行うための中央ハブとなります。 

- Azure Migrate を使用すると、Azure Migrate ツール、その他の Azure サービス、サードパーティ製のツールによって相互運用性と将来の拡張性が確保されます。
- Azure Migrate: Server Migration ツールは、サーバーを Azure に移行するための専用ツールです。 これは移行のために最適化されています。 移行とは直接関係ない概念やシナリオを理解する必要はありません。 
- VM のレプリケーションが開始された時点から 180 日間の移行に関して、ツールの使用料金は発生しません。 これにより、移行を完了する時間を確保できます。 料金は、レプリケーションで使用されるストレージとネットワークのリソース、およびテスト移行時に使用されるコンピューティング料金に対してのみ発生します。
- Azure Migrate では、Site Recovery でサポートされているすべての移行シナリオがサポートされます。 さらに、VMware VM の場合、Azure Migrate にはエージェントレスの移行オプションも用意されています。
- 新しい移行機能は、Azure Migrate: Server Migration ツールでのみ優先的に使用できます。 これらの機能は、Site Recovery を対象としたものではありません。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) は、ディザスター リカバリーにのみ使用する必要があります。

Azure Migrate: Server Migration ツールは、一部のオンプレミス マシンのリフトアンドシフト移行に、いくつかのバックエンド Site Recovery 機能を使用します。

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Azure Migrate の従来のエクスペリエンスを使用するプロジェクトがあります。 新しいバージョンを使い始めるにはどうすればよいですか。

従来の Azure Migrate は 2024 年 2 月に廃止されます。 2024 年 2 月以降、クラシック バージョンの Azure Migrate はサポートされなくなり、クラシック プロジェクトのインベントリ メタデータは削除されます。 旧バージョンのプロジェクトやコンポーネントを新バージョンにアップグレードすることはできません。 [新しい Azure Migrate プロジェクトを作成](create-manage-projects.md)して、それに[評価と移行ツールを追加する](./create-manage-projects.md)必要があります。 チュートリアルを利用して、使用可能な評価と移行ツールの使用方法を理解してください。 Log Analytics ワークスペースがクラシック プロジェクトにアタッチされている場合は、クラシック プロジェクトを削除した後で、そのワークスペースを現在のバージョンのプロジェクトにアタッチできます。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate:Server Assessment と Map Toolkit の違いは何ですか。

Server Assessment は、移行準備を支援する評価と、Azure への移行のためのワークロードの評価を提供します。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) は、新しいバージョンの Windows クライアントおよびサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡など、他のタスクに役立ちます。 そのような目的には、引き続き MAP Toolkit を使用します。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Server Assessment と Site Recovery Deployment Planner の違いは何ですか。

Server Assessment は移行計画ツールです。 Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

操作の目的に応じてツールを選択します。

- **オンプレミスの Azure への移行計画**:オンプレミスのサーバーを Azure に移行する予定がある場合は、移行計画に Server Assessment を使用します。 Server Assessment ではオンプレミスのワークロードが評価され、移行する際に役立つガイダンスとツールが提供されます。 移行計画の準備ができたら、Azure Migrate:Server Migration などのツールを使ってマシンを Azure に移行できます。
- **Azure へのディザスター リカバリーの計画**:Site Recovery を使用してオンプレミスから Azure へのディザスター リカバリーを設定する場合は、Site Recovery Deployment Planner を使用します。 Deployment Planner は、ディザスター リカバリーを目的として、オンプレミス環境に対する Site Recovery 独自の詳細な評価を提供します。 また、レプリケーションやフェールオーバーなどのディザスター リカバリーに関する推奨事項を提供します。

## <a name="how-does-server-migration-work-with-site-recovery"></a>Server Migration と Site Recovery の連動のしくみ

- Azure Migrate を使用する場合、Server Migration を使用して、オンプレミスの VMware VM の "*エージェントレス*" 移行を行った場合、移行が Azure Migrate に対してネイティブとなり、Site Recovery は使用されません。
- Azure Migrate を使用する場合、Server Migration を使用して、VMware VM の "*エージェントベースの*" 移行を実行するか、Hyper-V VM または物理サーバーを移行する場合、Azure Migrate:Server Migration は Azure Site Recovery のレプリケーション エンジンを使用します。

## <a name="which-geographies-are-supported"></a>サポートされている地域を教えてください。

[パブリック クラウド](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

## <a name="how-do-i-get-started"></a>開始するには?

必要なツールを特定し、次にそのツールを Azure Migrate プロジェクトに追加します。 

ISV ツールまたは Movere を追加する場合は、次のようにします。

1. ツールのポリシーに従って、ライセンスを取得するか、無料試用版にサインアップすることによって、使用を開始します。 ツールのライセンスは、ISV またはツールのライセンス モデルに準拠します。
2. 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示とドキュメントに従って、ツールを Azure Migrate に接続します。

Azure とその他のツール全体にわたって、Azure Migrate プロジェクト内から移行の過程を追跡できます。

## <a name="how-do-i-delete-a-project"></a>プロジェクトを削除する方法を教えてください。

[プロジェクトを削除する](how-to-delete-project.md)方法を確認してください。 

## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。