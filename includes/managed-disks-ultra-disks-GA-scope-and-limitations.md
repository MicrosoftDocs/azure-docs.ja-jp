---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aafeb7ebd939805ae18e29670f359660db12e2e1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555825"
---
現時点では、Ultra Disk には次のような追加の制限があります。

Ultra ディスクで現在利用できる結い位置のインフラストラクチャ冗長オプションは可用性ゾーンです。 他の冗長オプションを使用している VM では、Ultra ディスクを接続できません。

Ultra ディスクが利用できるリージョンとそれに該当する可用性オプションをまとめたものが次の表です。

> [!NOTE]
> 次の一覧のリージョンに、Ultra ディスク対応の可用性ゾーンがない場合、Ultra ディスクを接続するには、インフラストラクチャ冗長オプションなしで、そのリージョンの VM をデプロイする必要があります。

|リージョン  |冗長オプション  |
|---------|---------|
|Brazil South     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|Central India     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|東アジア     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|ドイツ中西部     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|韓国中部     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|米国中南部    |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|US Gov アリゾナ     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|US Gov バージニア州     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|US Gov テキサス     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|米国西部     |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)        |
|オーストラリア中部    |単一 VM のみ (可用性セットと仮想マシン スケール セットはサポートされません)|
|オーストラリア東部     |3 つの可用性ゾーン         |
|Southeast Asia    |3 つの可用性ゾーン        |
|カナダ中部     |3 つの可用性ゾーン          |
|米国中部     |3 つの可用性ゾーン          |
|米国東部     |3 つの可用性ゾーン          |
|米国東部 2     |3 つの可用性ゾーン         |
|フランス中部    |2 つの可用性ゾーン        |
|Japan East    |3 つの可用性ゾーン        |
|北ヨーロッパ    |3 つの可用性ゾーン        |
|英国南部    |3 つの可用性ゾーン        |
|西ヨーロッパ    | 3 つの可用性ゾーン|
|米国西部 2    |3 つの可用性ゾーン|

- 次の VM シリーズでのみサポートされています。
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません。
- データ ディスクとしてのみ使用できます。 
- 既定で 4k の物理セクター サイズがサポートされます。 512E セクター サイズは一般提供されているオファリングとして利用できます (サインアップは不要です)。 ほとんどのアプリケーションは 4k のセクター サイズと互換性がありますが、512 バイトのセクター サイズが必要になる場合があります。 1 つの例が、Oracle Database です。この場合、4k のネイティブ ディスクをサポートするには、リリース 12.2 以降が必要です。 以前のバージョンの Oracle DB では、512 バイトのセクター サイズが必要となります。
- 空のディスクとしてのみ作成できます。
- ディスク スナップショット、VM イメージ、可用性セット、Azure 専用ホスト、Azure Disk Encryption は現在のところサポートされていません。
- Azure Backup または Azure Site Recovery との統合は現在のところサポートされていません。
- キャッシュされていない読み取りとキャッシュされていない書き込みのみがサポートされます。
- GA VM での IOPS の現在の上限は 80,000 です。

Azure Ultra ディスクからは、既定でサブスクリプション別にリージョンあたり最大 32 TiB が提供されますが、Ultra ディスクでは要求があればそれを超える容量が提供されます。 容量の増加を依頼するには、クォータの増加を要求するか、Azure サポートにお問い合わせください。
