---
title: Azure SAP HANA のオペレーティング システムのアップグレード (ラージ インスタンス) | Microsoft Docs
description: Azure の SAP HANA のオペレーティング システムのアップグレードを実行します｡
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100756"
---
# <a name="operating-system-upgrade"></a>オペレーティング システムのアップグレード
このドキュメントでは､HANA ラージ インスタンスでのオペレーティング システムについての詳細を説明します｡

>[!NOTE]
>OS のアップグレードはお客様の責任です｡Microsoft の運用サポートができることは､アップグレード中にどのような点に注意を払うべきかについてお答えすることです｡ アップグレードを計画する前にオペレーティング システムのベンダーにも相談するようにしてください｡

HLI ユニットのプロビジョニング時､Microsoft の運用チームはオペレーティング システムをインストールします｡ 時間の経過と共に、HLI ユニット上でオペレーティング システムを保守することが必要になります (パッチ適用、チューニング、アップグレードなど)。

オペレーティング システムの大幅な変更 (SP1 から SP2 へのアップグレードなど) を行う前に、サポート チケットを開いて Microsoft Operations チームに連絡し、相談する必要があります。

チケットには次を含めてください。

* HLI サブスクリプション ID。
* サーバー名。
* 適用を予定しているパッチ レベル。
* この変更を予定している日付。 

運用チームがサーバー ブレードでファームウェア アップグレードが必要になるかどうかを確認できるように、希望するアップグレード日付の少なくとも 1 週間前にこのチケットを開くことをお勧めします。


各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。


## <a name="known-issues"></a>既知の問題

以下は､アップグレード中にいくつかある一般的な既知の問題です｡
- タイプ II クラスの SKU で､OS のアップグレード後に SFS が削除されます｡ OS のアップグレード後に互換性のある SFS を再インストールする必要があります｡
- イーサネット カードのドライバー (ENIC および FNIC) が古いバージョンにロールバックします｡ アップグレード後に互換性のあるドライバー バージョンを再インストールする必要があります｡

## <a name="next-steps"></a>次の手順
- OS バックアップのタイプ I SKU クラスについては､[バックアップと復元](hana-overview-high-availability-disaster-recovery.md)を参照してください｡
- タイプ II の SKU クラスについては[タイプ II SKU の OS バックアップ](os-backup-type-ii-skus.md) を参照してください｡
