---
title: SAP HANA on Azure (L インスタンス) のデータ階層化と拡張ノード | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のデータ階層化と拡張ノード。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666771"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA のデータ階層化と拡張ノードの使用

SAP では、さまざまな SAP NetWeaver リリースの SAP BW および SAP BW/4HANA のデータ階層化モデルをサポートしています。 データ階層化モデルの詳細については、SAP ドキュメントの「[SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes (SAP HANA 拡張ノードを持つ SAP BW/4HANA および SAP BW-on-HANA)](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)」をご覧ください。
HANA L インスタンスを使用すると、FAQ および SAP ブログ ドキュメントで説明する SAP HANA 拡張ノードのオプション 1 の構成をご利用いただけます。 オプション 2 の構成は次の HANA L インスタンス SKU を使用して設定できます: S72m、S192、S192m、S384、S384m。 

ドキュメントを参照しても、すぐには利点を確認できないかもしれません。 しかし、SAP のサイズ変更ガイドラインを見ると、オプション 1 およびオプション 2 の SAP HANA 拡張ノードを使用することによる利点を確認できます。 以下に例を示します。

- SAP HANA のサイズ変更ガイドラインでは通常、メモリの 2 倍のデータ ボリュームが必要です。 ホット データで SAP HANA インスタンスを実行した場合、メモリの 50% 以下しかデータが入っていません。 残りのメモリは、SAP HANA が作業を実行するために残しておくのが理想です。
- つまり、2 TB のメモリを持つ HANA L インスタンス S192 ユニットで SAP BW データベースを実行すると、データ ボリュームとしては 1 TB しかありません。
- オプション 1 の追加の SAP HANA 拡張ノードと S192 HANA L インスタンス SKU を使用した場合、データ ボリューム用に 2 TB の容量が追加されます。 オプション 2 の構成では、ウォーム データ ボリューム用に 4 TB が追加されます。 ホット ノードと比較した場合、オプション 1 ではデータの格納に "ウォーム" 拡張ノードの全メモリ容量をご利用いただけます。 オプション 2 の SAP HANA 拡張ノード構成では、データ ボリュームに 2 倍のメモリをご利用いただけます。
- その結果、オプション 1 ではデータ容量が 3 TB、ホットとウォームの割合が 1:2 になります。 オプション 2 の拡張ノード構成では、データ容量が 5 TB、ホットとウォームの割合が 1:4 になります。

メモリに対してデータ ボリュームが大きいほど、求めるウォーム データがディスク ストレージに保存されている可能性が高くなります。

**次の手順**
- [Azure の SAP HANA (L インスタンス) アーキテクチャ](hana-architecture.md)を参照してください