---
title: SAP HANA on Azure (L インスタンス) の SKU | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のSKU。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7a487b105c8edc34d9427de3b8ca6738da1855a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672104"
---
# <a name="available-skus-for-hli"></a>HLI で利用可能な SKU

リビジョン 3 スタンプのみに基づく SAP HANA on Azure (L インスタンス) サービスは、次の Azure リージョンで、複数の構成で使用できます。

- オーストラリア東部
- オーストラリア南東部
- 東日本
- 西日本

リビジョン 4 スタンプに基づく SAP HANA on Azure (L インスタンス) サービスは、次の Azure リージョンで、複数の構成で使用できます。

- 米国西部 2
- 米国東部
- 米国東部 2
- 米国中南部
- 西ヨーロッパ
- 北ヨーロッパ



[HANA L インスタンスの SAP HANA 認定 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 一覧は次のような内容です。

| SAP ソリューション | モデル | メモリ | ストレージ | 可用性 |
| --- | --- | --- | --- | --- |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA on Azure S96<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v4 <br /> 48 CPU コアと 96 CPU スレッド |  768 GB |  3.0 TB | 利用可能 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA on Azure S224<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド |  3.0 TB |  6.3 TB | 利用可能 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA on Azure S224m<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド |  6.0 TB |  10.5 TB | 利用可能 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  4.0 TB |  16 TB | 利用可能 |
|[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080)| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  6.0 TB |  18 TB | 利用可能 (リビジョン 4 のみ) |
| TDIv5 | SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  12.0 TB |  28 TB | 利用可能 |
|[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  8.0 TB |  22 TB |  利用可能  |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA on Azure S576m<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  12.0 TB |  28 TB | 利用可能 (リビジョン 4 のみ) |
| TDIv5 | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  18.0 TB |  41 TB | 利用可能 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA on Azure S768m<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  16.0 TB |  36 TB | 利用可能 (リビジョン 4 のみ) |
| TDIv5 | SAP HANA on Azure S768xm<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  24.0 TB |  56 TB | 利用可能 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA on Azure S960m<br /> – 20 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 480 CPU コアと 960 CPU スレッド |  20.0 TB |  46 TB | 利用可能 (リビジョン 4 のみ) |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA on Azure S896m<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 24.0 TB | 35.8 TB | 利用可能 (リビジョン 4 のみ) |

- CPU コア = サーバー ユニットのプロセッサの合計の、非ハイパースレッド CPU コアの合計。
- CPU スレッド = サーバー ユニットのプロセッサの合計の、ハイパースレッド CPU コアによって提供されるコンピューティング スレッドの合計。 ほとんどのユニットは、ハイパースレッディング テクノロジを使用するように既定で構成されます。
- サプライヤーの推奨事項に基づいて、S768m、S768xm、および S960m は、SAP HANA の実行にハイパースレッディングを使用するように構成されていません。


SAP HANA TDIv5 の場合、SAP を使用してユーザー固有のサイズとユーザー固有のプロジェクトを構成できるので、認定と見なされないサーバー構成になる可能性があります。

- [SAP HANA 認定アプライアンス](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

多くの場合、このようなユーザー固有のサーバー構成は、SAP で認定されているサーバー ユニットよりも多くのメモリを搭載しています。 ユーザーは SAP と連携して、ユーザー固有のサイズのサーバー構成について SAP のサポートを受け、認定される可能性があります。 

また、次の L インスタンス Standard SKU は、まだ SAP で認定されていませんが、利用可能です。Microsoft の価格表に記載されており、ご購入いただけます。

 | モデル | メモリの合計 | メモリ DRAM | メモリ Optane | ストレージ | 可用性 |
| --- | --- | --- | --- | --- | --- |
| SAP HANA on Azure S224oo<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド | 4.5 TB |  1.5 TB |  3.0 TB | 8.4 TB | 利用可能 |
| SAP HANA on Azure S224om<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド | 6.0 TB |  3.0 TB |  3.0 TB | 10.5 TB | 利用可能 |
| SAP HANA on Azure S224ooo<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド | 7.5 TB |  1.5 TB |  6.0 TB | 12.7 TB | 利用可能 |
| SAP HANA on Azure S224oom<br /> \- 4 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 112 CPU コアと 224 CPU スレッド | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB | 利用可能 |
| SAP HANA on Azure S448<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 6.0 TB |  6.0 TB |  --- | 10.5 TB | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S448m<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S448oo<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S448om<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 12.0 TB |  6.0 TB |  6.0 TB | 18.9 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S448ooo<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 15.0 TB |  3.0 TB |  12.0 TB | 23.2 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S448oom<br /> \- 8 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 224 CPU コアと 448 CPU スレッド | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 9.0 TB |  9.0 TB |  --- | 14.7 TB | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672m<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 18.0 TB |  18.0 TB |  --- | 27.4 TB | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672oo<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 13.5 TB |  4.5 TB |  9.0 TB | 21.1 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672om<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 18.0 TB |  9.0 TB |  9.0 TB | 27.4 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672ooo<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 22.5 TB |  4.5 TB |  18.0 TB | 33.7 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S672oom<br /> \- 12 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 336 CPU コアと 672 CPU スレッド | 27.0 TB |  9.0 TB |  18.0 TB | 40.0 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S896<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S896oo<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S896om<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 24.0 TB |  12.0 TB |  12.0 TB | 35.8 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S896ooo<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 30.0 TB |  6.0 TB |  24.0 TB | 44.3 TB  | 利用可能 (リビジョン 4 のみ) |
| SAP HANA on Azure S896oom<br /> \- 16 x Intel® Xeon® プラチナ 8276 プロセッサ <br /> 448 CPU コアと 896 CPU スレッド | 36.0 TB |  12.0 TB |  24.0 TB | 52.7 TB  | 利用可能 (リビジョン 4 のみ) |


> [!IMPORTANT]
> 次の SKU は、まだサポートされていますが、ご購入いただけなくなりました。S72、S72m、S144、S144m、S192、S192m 

選択する構成は、ワークロード、CPU リソース、および必要なメモリによって異なります。 OLTP ワークロードでは、OLAP ワークロードに最適化された SKU をご利用いただけます。 

ユーザー固有のサイズ設定プロジェクトのユニットを除き、オファーのハードウェア ベースは SAP HANA TDI 認定です。 ハードウェアの 2 種類のクラスでは、SKU を次のように分けています。

- S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m、S224oo、S224om、S224ooo、S224oom は、"Type I クラス" の SKU と呼ばれます。
- その他のすべての SKU は、SKU の "Type II クラス" と呼ばれます。
- SAP ハードウェア ディレクトリにまだ掲載されていない SKU に関心をお持ちの場合は、Microsoft アカウント チームに問い合わせて、詳細情報を確認してください。 


完全な HANA L インスタンス スタンプは、1 件のお客様専用に割り当てられるわけではありません。 これは、Azure にデプロイされたネットワーク ファブリック経由で接続されるコンピューティング リソースとストレージ リソースのラックにも当てはまります。 Azure などの HANA L インスタンス インフラストラクチャでは、次の 3 つのレベルで相互に分離されたお客様のさまざまな &quot;テナント&quot; をデプロイします。

- **ネットワーク**:HANA L インスタンス スタンプ内の仮想ネットワークによって分離します。
- **ストレージ**: ストレージ ボリュームが割り当てられており、テナント間でストレージ ボリュームを分離するストレージ仮想マシンによって分離します。
- **コンピューティング**:サーバー ユニットを 1 つのテナント専用に割り当てます。 サーバー ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 1 つのサーバー ユニットやホスト ユニットがテナント間で共有されることもありません。 

異なるテナント間で HANA L インスタンス ユニットのデプロイが相互に表示されることはありません。 異なるテナントにデプロイされた HANA L インスタンス ユニットは、HANA L インスタンス スタンプ レベルで相互に直接通信することはできません。 HANA L インスタンス スタンプ レベルでは、同じテナント内の HANA L インスタンス ユニットだけが相互に通信できます。

L インスタンス スタンプにデプロイされたテナントは、課金のために 1 つの Azure サブスクリプションに割り当てられます。 ネットワークについては、同じ Azure 加入契約内の他の Azure サブスクリプションの仮想ネットワークからアクセスできます。 同じ Azure リージョンの別の Azure サブスクリプションを使用してデプロイする場合は、分離された HANA L インスタンス テナントを要求することもできます。

HANA L インスタンスで実行される SAP HANA と、Azure にデプロイされた VM で実行される SAP HANA には大きな違いがあります。

- SAP HANA on Azure (L インスタンス) の仮想化層はありません。 基になるベア メタル ハードウェアのパフォーマンスを確保します。
- Azure とは異なり、SAP HANA on Azure (L インスタンス) サーバーは特定のお客様に専用のサーバーです。 サーバー ユニットまたはホスト ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 そのため、HANA L インスタンス ユニットは、その全体がテナントに割り当てられ、テナントがお客様に割り当てられて使用されます。 サーバーを再起動またはシャットダウンしても、オペレーティング システムと SAP HANA が別のサーバーに自動的にデプロイされることはありません (Type I クラスの SKU の場合、唯一の例外は、サーバーで問題が発生し、別のサーバーで再デプロイを実行する必要がある場合です)。
- Azure とは異なり、価格/パフォーマンス比が最も高くなるようにホスト プロセッサの種類を選択する場合は、Intel E7v3 および E7v4 プロセッサ ラインの最もパフォーマンスが高いプロセッサが SAP HANA on Azure (L インスタンス) 用に選択されます。

**次の手順**
- [HLI Sizing](hana-sizing.md)を参照
