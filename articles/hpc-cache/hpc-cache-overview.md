---
title: Azure HPC Cache プレビューの概要
description: ハイパフォーマンス コンピューティングのためのファイル アクセス アクセラレータ ソリューションである Azure HPC Cache について説明します。
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: bfbbcd2d7d1bb44e260bedda54ca38ed6860ea67
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254486"
---
# <a name="what-is-azure-hpc-cache-preview"></a>Azure HPC Cache とは (プレビュー)

ハイパフォーマンス コンピューティング (HPC) タスクには、Azure HPC Cache を使用することでデータへのアクセスを高速化できます。 Azure HPC Cache で Azure 内のファイルをキャッシュすることで、クラウド コンピューティングのスケーラビリティを既存のワークフローでも実現します。 このサービスは、ローカル データセンターのネットワーク接続ストレージ (NAS) 環境などの WAN リンクにデータが格納されているワークフローでも使用できます。

Azure HPC Cache は、Azure portal から簡単に立ち上げて監視することができます。 既にある NFS ストレージや新しい BLOB コンテナーをその集約された名前空間に追加できるため、バックエンドのストレージ ターゲットを変更する場合でも、クライアント アクセスはシンプルになります。

## <a name="use-cases"></a>ユース ケース

Azure HPC Cache による生産性の向上は、次のようなワークフローで最大限に発揮されます。

* 読み取り負荷の高いファイル アクセス ワークフロー
* NFS でアクセスできるストレージ、Azure BLOB、またはその両方に格納されるデータ
* CPU のコア数が 75,000 個までのコンピューティング ファーム

Azure HPC Cache は、業種を越えてさまざまなワークフローに取り入れることができます。 多数のマシンが一連のファイルに低遅延で、かつ大規模にアクセスする必要のあるすべてのシステムで、このサービスは利便性を発揮します。 以降のセクションで具体的な例を示します。

### <a name="visual-effects-vfx-rendering"></a>視覚効果 (VFX) のレンダリング

メディアとエンターテイメントの分野では、タイムクリティカルなレンダリング プロジェクトのデータ アクセスを Azure HPC Cache によってスピードアップできます。 VFX レンダリング ワークフローでは、多数のコンピューティング ノードによる最終処理が必要になることが少なくありません。 そうしたワークフローに使用されるデータは通常、オンプレミスの NAS 環境に置かれます。 Azure HPC Cache では、そのファイル データをクラウドにキャッシュできるため、待ち時間が減り、柔軟性が向上して、オンデマンド レンダリングが実現します。

### <a name="life-sciences"></a>ライフ サイエンス

多くのライフ サイエンス ワークフローは、スケールアウト ファイル キャッシュの恩恵を受けることができます。

ゲノム解析ワークフローを Azure に移植したいと考えている研究施設は、Azure HPC Cache を使用することで簡単にシフトすることができます。 キャッシュには POSIX ファイル アクセスが用意されているため、既存のクライアント ワークフローをクラウドで実行するためにクライアント側の変更は必要ありません。

二次解析、薬理学的シミュレーション、AI による画像分析などのタスクについても、Azure HPC Cache を活用して効率を高めることができます。

### <a name="financial-services-analytics"></a>金融サービスの分析

Azure HPC Cache のデプロイは定量分析計算やリスク分析ワークロード、モンテカルロ シミュレーションをスピードアップするために役立ちます。これにより、金融サービス会社はより優れた分析情報を得て戦略的な決定を行えます。

## <a name="region-availability"></a>利用可能なリージョン

Azure HPC Cache は、次の Azure リージョンで使用できます。

* East US
* 米国東部 2
* 北ヨーロッパ
* 西ヨーロッパ
* 東南アジア
* 米国西部 2

最新情報については、[Azure HPC Cache の製品ページ](https://azure.microsoft.com/services/hpc-cache)をご覧ください。

## <a name="preview-availability"></a>プレビューのリリース状況

サービスの品質を保証するために、Azure HPC Cache パブリック プレビューは制限されています。 アクセスを依頼するには、[こちらのフォーム](https://aka.ms/onboard-hpc-cache)に入力してください。 サブスクリプションがアクセス一覧に追加された後に、テスト キャッシュを作成できます。

## <a name="next-steps"></a>次の手順

* [Azure HPC Cache の製品ページ](https://azure.microsoft.com/services/hpc-cache)を参照して、その機能を詳しく確認する
* 製品の[前提条件](hpc-cache-prereqs.md)を確認する
* Azure portal から [Azure HPC Cache を作成](hpc-cache-create.md)する
