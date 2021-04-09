---
title: SAP HANA on Azure (L インスタンス) のストレージ アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のストレージ アーキテクチャ。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff0a674e4f07dd38eaab2ffdbafe1f39e44e6ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676843"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (Large Instances) のストレージ アーキテクチャ

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP 推奨ガイドラインに従い、クラシック デプロイ モデルで SAP HANA によって構成されます。 これらのガイドラインは、ホワイト ペーパー「[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」に記載されています。

Type I クラスの HANA L インスタンスは、ストレージ ボリュームの 4 倍のメモリ ボリュームを備えています。 Type II クラスの HANA L インスタンス ユニットの場合、ストレージが 4 倍以上になることはありません。 これらのユニットは、HANA トランザクション ログ バックアップを保存するためのボリュームを備えています。 詳細については、[SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md)に関する記事をご覧ください。

記憶域割り当てに関しては、次の表を参照してください。 この表は、各種 HANA L インスタンス ユニットで提供されるさまざまなボリュームのおおよその容量を示しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |
| S896m | 33,792 GB | 512 GB | 1,024 GB | 512 GB |

HANA Large Instances の最近の SKU は次のようなストレージ構成で提供されています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4,224 GB | 512 GB | 1,024 GB | 512 GB |
| S224oo | 6,336 GB | 512 GB | 1,024 GB | 512 GB |
| S224m | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224om | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224ooo | 10,560 GB | 512 GB | 1,024 GB | 512 GB |
| S224oom | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448 | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S448oo | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448m | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448om | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448ooo | 21,120 GB | 512 GB | 1,024 GB | 512 GB |
| S448oom | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672 | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S672oo | 19,008 GB | 512 GB | 1,024 GB | 512 GB |
| S672m | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672om | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672ooo | 31,680 GB | 512 GB | 1,024 GB | 512 GB |
| S672oom | 38,016 GB | 512 GB | 1,024 GB | 512 GB |
| S896 | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S896oo | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S896om | 33,792 GB | 512 GB | 1,024 GB | 512 GB |
| S896ooo | 42,240 GB | 512 GB | 1,024 GB | 512 GB |
| S896oom | 50,688 GB | 512 GB | 1,024 GB | 512 GB |


実際にデプロイされるボリュームは、デプロイと、ボリューム サイズの表示に使用するツールによって異なる場合があります。

HANA L インスタンス SKU を分割する場合、考えられる各分割部分の例は次のようになります。

| メモリ パーティション (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


これらのサイズはおおよそのボリューム数であり、デプロイと、ボリュームの確認に使用するツールによって若干異なる場合があります。 他のパーティション サイズ (2.5 TB など) もあります。 これらのストレージ サイズは、上記のパーティションに使用したものと同様の数式で計算されます。 "パーティション" という用語は、オペレーティング システム、メモリ、または CPU リソースがパーティション分割されていることを意味するわけではありません。 これは、1 つの HANA L インスタンス ユニットにデプロイできるさまざまな HANA インスタンスのストレージ パーティションを示しています。 

ストレージを増やすことが必要な場合があります。 ストレージを追加するには、1 TB 単位で追加のストレージを購入します。 この追加のストレージは、ボリュームとして追加できます。 また、1 つ以上の既存のボリュームを拡張するために使用することもできます。 もともとデプロイされているボリュームのサイズ (上記の表に記載) を減らすことはできません。 また、ボリュームの名前やマウント名を変更することもできません。 上記のストレージ ボリュームは、NFS4 ボリュームとして HANA L インスタンス ユニットに接続されます。

バックアップ/復元とディザスター リカバリーのために、ストレージ スナップショットをご利用いただけます。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」をご覧ください。

ご使用のシナリオの記憶域レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>1 つの HANA L インスタンス ユニットでの複数の SAP HANA インスタンスの実行

HANA L インスタンス ユニットで複数のアクティブな SAP HANA インスタンスをホストできます。 ストレージ スナップショットとディザスター リカバリーの機能を提供するために、このような構成ではインスタンスごとにボリューム セットが必要になります。 現時点では、HANA L インスタンス ユニットを次のように分けることができます。

- **S72、S72m、S96、S144、S192**:256 GB の最小ユニットから 256 GB ずつ増加。 256 GB、512 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **S144m および S192m**:512 GB の最小ユニットから 256 GB ずつ増加。 512 GB、768 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **Type II クラス**:2 TB の最小ユニットから 512 GB ずつ増加。 512 GB、1 TB、1.5 TB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。

複数の SAP HANA インスタンスの実行例は次のようになります。

| SKU | メモリ サイズ | ストレージ サイズ | 複数のデータベースとサイズ |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 768 GB HANA インスタンス x 1<br /> または 512 GB インスタンス x 1 + 256 GB インスタンス x 1<br /> または 256 GB インスタンス x 3 | 
| S72m | 1.5 TB | 6 TB | 512 GB HANA インスタンス x 3<br />または 512 GB インスタンス x 1 + 1 TB インスタンス x 1<br />または 256 GB インスタンス x 6<br />または 1.5 TB インスタンス x 1 | 
| S192m | 4 TB | 16 TB | 512 GB インスタンス x 8<br />または 1 TB インスタンス x 4<br />または 512 GB インスタンス x 4 + 1 TB インスタンス x 2<br />または 768 GB インスタンス x 4 + 512 GB インスタンス x 2<br />または 4 TB インスタンス x 1 |
| S384xm | 8 TB | 22 TB | 2 TB インスタンス x 4<br />または 4 TB インスタンス x 2<br />または 3 TB インスタンス x 2 + 2 TB インスタンス x 1<br />または 2.5 TB インスタンス x 2 + 3 TB インスタンス x 1<br />または 8 TB インスタンス x 1 |


他のバリエーションもあります。 

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA L インスタンスに使用されるストレージでは、2018 年末以降、データがディスクに格納されているため、データに対して透過的な暗号化が使用されます。 以前のデプロイでは、ボリュームを暗号化するように選択できました。 そのオプションを使用していない場合は、オンラインで暗号化されたボリュームを取得するように要求できます。 暗号化されていないボリュームから暗号化されたボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 

Type I クラスの SKU では、ブート LUN が格納されているボリュームが暗号化されます。 Hana L インスタンスの SKU の Type II クラスを使用しているリビジョン 3 HANA L インスタンス スタンプでは、OS 方式でブート LUN を暗号化する必要があります。 Type II ユニットを使用しているリビジョン 4 HANA L インスタンス スタンプでは、ブート LUN が格納されているボリュームも、既定で保存時に暗号化されます。 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA L インスタンス上のさらに大きい HANA インスタンスに対する必要な設定
HANA L インスタンスで使用されるストレージには、ファイル サイズの制限があります。 ファイルごとの[サイズの制限は 16 TB です](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 EXT3 ファイル システムのファイル サイズ制限とは異なり、HANA は、HANA L インスタンスのストレージによって強制されるストレージ制限を暗黙的に認識しません。 そのため、HANA ではファイル サイズ制限の 16 TB に達したときに新しいデータ ファイルが自動的に作成されません。 HANA は 16 TB を超えるサイズへファイルを拡大しようとするため、HANA でエラーが報告され、最終的にはインデックス サーバーがクラッシュします。

> [!IMPORTANT]
> HANA L インスタンス ストレージの 16 TB のファイル サイズ制限を超えて、HANA がデータ ファイルの拡大を試行するのを防ぐには、HANA の global.ini 構成ファイルで以下のパラメーターを設定する必要があります
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005) も参照してください
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285) に注意してください




**次の手順**
- [HANA L インスタンスのサポートされるシナリオ](hana-supported-scenario.md)を参照