---
title: Azure Stream Analytics クラスターの概要
description: Stream Analytics クラスターのシングル テナント専用オファリングについて説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709293"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Azure Stream Analytics クラスターの概要

Azure Stream Analytics クラスターでは、複雑で要求の厳しいストリーミング シナリオ向けのシングルテナント デプロイが提供されます。 フル スケールでは、Stream Analytics クラスターは、リアルタイムで 200 MB/秒より速く処理できます。 専用クラスター上で実行されている Stream Analytics ジョブでは、標準オファリングのすべての機能を利用でき、入力と出力へのプライベート リンク接続がサポートされます。

Stream Analytics クラスターは、クラスターに割り当てられた CPU とメモリのリソース量を表すストリーミング ユニット (SU) ごとに課金されます。 ストリーミング ユニットは、標準と専用のどちらのオファリングでも同じです。 クラスターごとに 36、72、108、144，180、または 216 の SU を購入することができます。 Stream Analytics クラスターは、組織のストリーミング プラットフォームとして機能でき、さまざまなユース ケースに対応するさまざまなチームが共有できます。

## <a name="what-are-stream-analytics-clusters"></a>Stream Analytics クラスターとは

Stream Analytics クラスターには、マルチテナント環境で実行される Stream Analytics ジョブに使用されるものと同じエンジンが搭載されています。 シングル テナントの専用クラスターには、次の機能があります。

* 他のテナントからのノイズがないシングル テナント ホスティング。 リソースは、実際に "分離" され、トラフィックにバーストがある場合のパフォーマンスが向上します。

* ストリーミングの使用量は時間の経過と共に増加するため、クラスターを 36 から 216 の SU にスケーリングします。

* Stream Analytics ジョブがプライベート エンドポイントを使用して他のリソースに安全に接続できるようにする VNet サポート。

* 任意のリージョン内で C# ユーザー定義関数とカスタム逆シリアライザーを作成する機能。

* リアルタイム分析ソリューションの構築に専念できるようにする、ゼロ メンテナンス コスト。

## <a name="how-to-get-started"></a>ファースト ステップ

[Azure portal](https://aka.ms/asaclustercreateportal) を使用して、[Stream Analytics クラスターを作成](create-cluster.md)することができます。 ご質問がある場合や、オンボードに関するヘルプが必要な場合は、[Stream Analytics チーム](mailto:askasa@microsoft.com)にお問い合わせください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Stream Analytics クラスターと Stream Analytics ジョブの間の選択はどのようにして行いますか?

作業を開始する最も簡単な方法は、サービスに慣れるために Stream Analytics ジョブを作成して開発し、分析要件を満たす方法を確認することです。

Stream Analytics ジョブだけでは VNet はサポートされません。 入力または出力がファイアウォールまたは Azure Virtual Network の内側でセキュリティ保護されている場合は、次の 2 つのオプションがあります。

* ローカル コンピューターが VNet (Azure Event Hubs や Azure SQL Database など) によって保護された入力および出力リソースにアクセスできる場合は、ローカル コンピューターに [Visual Studio 用の Azure Stream Analytics ツールをインストール](stream-analytics-tools-for-visual-studio-install.md)することができます。 コストを発生させることなく、デバイス上で [Stream Analytics ジョブをローカルに開発およびテスト](stream-analytics-live-data-local-testing.md)できます。 アーキテクチャ内で Stream Analytics を使用する準備ができたら、Stream Analytics クラスターを作成し、プライベート エンドポイントを構成して、ジョブを大規模に実行できます。

* Stream Analytics クラスターを作成して、パイプラインに必要なプライベート エンドポイントを使用してクラスターを構成し、クラスター上で Stream Analytics ジョブを実行できます。

### <a name="what-performance-can-i-expect"></a>どのようなパフォーマンスを期待できますか?

SU は、標準と専用のどちらのオファリングでも同じです。 完全な 36 SU クラスターを利用する 1 つのジョブでは、ミリ秒の待機時間で約 36 MB/秒のスループットを実現できます。 正確な数値は、イベントの形式と分析の種類によって決まります。 Stream Analytics クラスターは専用であるため、より信頼性の高いパフォーマンス保証が提供されます。 クラスター上で実行されているすべてのジョブは自分だけに所属します。

### <a name="can-i-scale-my-cluster"></a>クラスターをスケーリングできますか?

はい。 クラスターの容量を簡単に構成して、需要の変化に対応するために必要に応じて[スケールアップまたはスケールダウン](scale-cluster.md)できます。

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>作成した新しいクラスターで既存のジョブを実行できますか?

はい。 新しく作成した Stream Analytics クラスターに既存のジョブをリンクし、通常どおりに実行することができます。 既存の Stream Analytics ジョブを最初から再作成する必要はありません。

### <a name="how-much-will-these-clusters-cost-me"></a>これらのクラスターにはどのくらいのコストがかかりますか?

Stream Analytics クラスターは、選択した SU 容量に基づいて課金されます。 クラスターは時間単位で課金され、これらのクラスター内で実行されるジョブごとに追加料金は発生しません。 プライベート エンドポイントの課金の更新については、[Private Link サービスの価格に関するページ](https://azure.microsoft.com/pricing/details/private-link/)を参照してください。

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Stream Analytics クラスターから、どの入力と出力をプライベートに接続できますか?

Stream Analytics では、さまざまな種類の入力と出力がサポートされます。 クラスター内に、ジョブが入力および出力リソースにアクセスできるようにする[プライベート エンドポイントを作成](private-endpoints.md)できます。 現時点では、サポートされているサービスは、Azure SQL Database、Azure Storage、Azure Data Lake Storage Gen2、Azure Event Hubs、Azure Service Bus ですが、他の種類も間もなく追加される予定です。 

## <a name="next-steps"></a>次のステップ

Azure Stream Analytics クラスターの概要は以上です。 次に、クラスターを作成し、Stream Analytics ジョブを実行できます。 

* [Stream Analytics クラスターの作成](create-cluster.md)
* [Azure Stream Analytics クラスター内でプライベート エンドポイントを管理する](private-endpoints.md)
* [Stream Analytics クラスター内で Stream Analytics ジョブを管理する](manage-jobs-cluster.md)
* [Stream Analytics のジョブの作成](stream-analytics-quick-create-portal.md)
