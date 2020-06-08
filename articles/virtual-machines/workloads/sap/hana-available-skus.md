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
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617259"
---
# <a name="available-skus-for-hli"></a>HLI で利用可能な SKU

リビジョン 3 スタンプに基づく SAP HANA on Azure (L インスタンス) サービスは、次の Azure リージョンで、複数の構成で使用できます。

- 米国西部
- 米国東部
- オーストラリア東部
- オーストラリア南東部
- 西ヨーロッパ
- 北ヨーロッパ
- 東日本
- 西日本

リビジョン 4 スタンプに基づく SAP HANA on Azure (L インスタンス) サービスは、次の Azure リージョンで、複数の構成で使用できます。

- 米国西部 2
- 米国東部
- 米国中南部
- 西ヨーロッパ
- 北ヨーロッパ



[HANA L インスタンスの SAP HANA 認定 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 一覧は次のような内容です。

| SAP ソリューション | CPU | メモリ | ストレージ | 可用性 |
| --- | --- | --- | --- | --- |
| OLAP 用に最適化: SAP BW、BW/4HANA、<br /> または SAP HANA (一般的な OLAP ワークロード向け) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  768 GB |  3 TB | 提供終了 |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  1.5 TB |  6 TB | 提供終了 |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド |  2.0 TB |  8 TB | 提供終了 |
| --- | SAP HANA on Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276 プロセッサ (Cascade lake とも呼ばれます)<br /> 112 CPU コアと 224 CPU スレッド |  3.0 TB |  6.3 TB | Revision3 スタンプと Revision4 スタンプで使用可能  |
| --- | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  4.0 TB |  16 TB | Revision4 スタンプで使用可能 |
| OLTP 用に最適化: SAP Business Suite<br /> on SAP HANA または S/4HANA (OLTP)、<br /> 一般的な OLTP | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  1.5 TB |  6 TB | 提供終了 |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  3.0 TB |  12 TB | 提供終了 |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド  |  4.0 TB |  16 TB | 提供終了 |
| --- | SAP HANA on Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276 プロセッサ (Cascade lake とも呼ばれます)<br /> 112 CPU コアと 224 CPU スレッド |  6.0 TB |  10.5 TB | Revision3 スタンプと Revision4 スタンプで使用可能  |
|---| SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  6.0 TB |  18 TB | Revision4 スタンプで使用可能|
|---| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  8.0 TB |  22 TB |  Revision4 スタンプで使用可能 |
|---| SAP HANA on Azure S576m<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  12.0 TB |  28 TB | Revision4 スタンプで使用可能|
|---| SAP HANA on Azure S768m<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  16.0 TB |  36 TB | Revision4 スタンプで使用可能|
|---| SAP HANA on Azure S960m<br /> – 20 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 480 CPU コアと 960 CPU スレッド |  20.0 TB |  46 TB | Revision4 スタンプで使用可能|


SAP HANA TDIv5 の場合、SAP を使用してユーザー固有のサイズとユーザー固有のプロジェクトを構成できるので、認定と見なされないサーバー構成になる可能性があります。

- [SAP HANA 認定アプライアンス](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

多くの場合、このようなユーザー固有のサーバー構成は、SAP で認定されているサーバー ユニットよりも多くのメモリを搭載しています。 ユーザーは SAP と連携して、ユーザー固有のサイズのサーバー構成について SAP のサポートを受け、認定される可能性があります。 Azure では、次の HANA L インスタンス Standard SKU を利用できます。また、このような TDIv5 のユーザー固有のサイズ設定プロジェクトについては、Microsoft の価格表を参照してください。

| SKU|CPU | メモリ | ストレージ | 可用性 |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA on Azure S96<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 48 CPU コアと 96 CPU スレッド |  768 GB |  3 TB | Revision3 スタンプと Revision4 スタンプで使用可能|


| メモリ内の拡張可能な <br /> 元の SKU | CPU | メモリ | ストレージ | 可用性 |
| --- | --- | --- | --- | --- |
| S192m を拡張可能 | SAP HANA on Azure S192xm<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド |  6.0 TB |  16 TB | 提供終了 |
| S384xm を拡張可能 | SAP HANA on Azure S384xxm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  12.0 TB |  28 TB | Revision4 スタンプで使用可能 |
| S576m を拡張可能 | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  18.0 TB |  41 TB | Revision4 スタンプで使用可能|
| S768m を拡張可能 | SAP HANA on Azure S768xm<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  24.0 TB |  56 TB | Revision4 スタンプで使用可能 |

- CPU コア = サーバー ユニットのプロセッサの合計の、非ハイパースレッド CPU コアの合計。
- CPU スレッド = サーバー ユニットのプロセッサの合計の、ハイパースレッド CPU コアによって提供されるコンピューティング スレッドの合計。 ほとんどのユニットは、ハイパースレッディング テクノロジを使用するように既定で構成されます。
- サプライヤーの推奨事項に基づいて、S768m、S768xm、および S960m は、SAP HANA の実行にハイパースレッディングを使用するように構成されていません。


選択する構成は、ワークロード、CPU リソース、および必要なメモリによって異なります。 OLTP ワークロードでは、OLAP ワークロードに最適化された SKU をご利用いただけます。 

ユーザー固有のサイズ設定プロジェクトのユニットを除き、オファーのハードウェア ベースは SAP HANA TDI 認定です。 ハードウェアの 2 種類のクラスでは、SKU を次のように分けています。

- S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m は、"Type I クラス" の SKU と呼ばれます。
- S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m は、"Type II クラス" の SKU と呼ばれます。
- Optane で 4.5 TB から 9 TB を提供する他の S224 SKU に関心がある場合は、Microsoft アカウント チームに問い合わせて、詳細情報を入手してください。 


完全な HANA L インスタンス スタンプは、1 件のお客様専用に割り当てられるわけではありません。 これは、Azure にデプロイされたネットワーク ファブリック経由で接続されるコンピューティング リソースとストレージ リソースのラックにも当てはまります。 Azure などの HANA L インスタンス インフラストラクチャでは、次の 3 つのレベルで相互に分離されたお客様のさまざまな &quot;テナント&quot; をデプロイします。

- **ネットワーク**: HANA L インスタンス スタンプ内の仮想ネットワークによって分離します。
- **ストレージ**: ストレージ ボリュームが割り当てられており、テナント間でストレージ ボリュームを分離するストレージ仮想マシンによって分離します。
- **コンピューティング**: サーバー ユニットを 1 つのテナント専用に割り当てます。 サーバー ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 1 つのサーバー ユニットやホスト ユニットがテナント間で共有されることもありません。 

異なるテナント間で HANA L インスタンス ユニットのデプロイが相互に表示されることはありません。 異なるテナントにデプロイされた HANA L インスタンス ユニットは、HANA L インスタンス スタンプ レベルで相互に直接通信することはできません。 HANA L インスタンス スタンプ レベルでは、同じテナント内の HANA L インスタンス ユニットだけが相互に通信できます。

L インスタンス スタンプにデプロイされたテナントは、課金のために 1 つの Azure サブスクリプションに割り当てられます。 ネットワークについては、同じ Azure 加入契約内の他の Azure サブスクリプションの仮想ネットワークからアクセスできます。 同じ Azure リージョンの別の Azure サブスクリプションを使用してデプロイする場合は、分離された HANA L インスタンス テナントを要求することもできます。

HANA L インスタンスで実行される SAP HANA と、Azure にデプロイされた VM で実行される SAP HANA には大きな違いがあります。

- SAP HANA on Azure (L インスタンス) の仮想化層はありません。 基になるベア メタル ハードウェアのパフォーマンスを確保します。
- Azure とは異なり、SAP HANA on Azure (L インスタンス) サーバーは特定のお客様に専用のサーバーです。 サーバー ユニットまたはホスト ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 そのため、HANA L インスタンス ユニットは、その全体がテナントに割り当てられ、テナントがお客様に割り当てられて使用されます。 サーバーを再起動またはシャットダウンしても、オペレーティング システムと SAP HANA が別のサーバーに自動的にデプロイされることはありません (Type I クラスの SKU の場合、唯一の例外は、サーバーで問題が発生し、別のサーバーで再デプロイを実行する必要がある場合です)。
- Azure とは異なり、価格/パフォーマンス比が最も高くなるようにホスト プロセッサの種類を選択する場合は、Intel E7v3 および E7v4 プロセッサ ラインの最もパフォーマンスが高いプロセッサが SAP HANA on Azure (L インスタンス) 用に選択されます。

**次の手順**
- [HLI Sizing](hana-sizing.md)を参照
