---
title: "StorSimple 8000 シリーズ Update 5 リリース ノート | Microsoft Docs"
description: "StorSimple 8000 シリーズ Update 5 における新機能、問題、回避策について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/23/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e68dce72d648171faab930bbb4af9fd61816b19b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 シリーズ Update 5 リリース ノート

## <a name="overview"></a>概要

次のリリース ノートでは、StorSimple 8000 シリーズの Update 5 の新機能について説明し、未解決の重要な問題を示します。 また、このリリースに含まれる StorSimple のソフトウェアの更新プログラムの一覧も含まれています。

Update 5 は、Update 0.1 から Update 4 までを実行しているすべての StorSimple デバイスに適用できます。 Update 5 に関連付けられているデバイスのバージョンは、6.3.9600.17845 です。

StorSimple ソリューションの更新プログラムをデプロイする前に、リリース ノートに含まれる情報を確認してください。

> [!IMPORTANT]
> * Update 5 は、デバイス ソフトウェア、ディスク ファームウェア、OS セキュリティなどの OS 更新プログラムを備えています。 この更新プログラムのインストールには 4 時間程度かかります。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであり、デバイスのダウンタイムが発生します。 Update 5 を適用してデバイスを最新の状態に保つことをお勧めします。
> * 新しいリリースについては、更新プログラムの公開を段階的に行うため、更新プログラムが即座に表示されない場合があります。 これらの更新プログラムは間もなく利用可能になるため、数日待ってから、もう一度、更新プログラムの有無を確認してください。

## <a name="whats-new-in-update-5"></a>Update 5 の新機能

Update 5 では、主に次の機能強化とバグ修正が行われています。

* **StorSimple デバイス マネージャー サービスで認証するための Azure Active Directory (AAD) の使用** – Update 5 以降、StorSimple デバイス マネージャーサービスでの認証には Azure Active Directory が使用されています。 従来の認証機構は、2017 年 12 月で廃止される予定です。 すべてのユーザーは、ファイアウォール規則に新しい認証 URL を含める必要があります。 詳細については、[StorSimple デバイスのネットワーク要件に記載されている認証 URL ](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)をご確認ください。

    認証 URL がファイアウォール規則に含まれていない場合、StorSimple デバイスがサービスで認証できなかったことを示す重要なアラートが、ユーザーに表示されます。 このアラートが表示されると、ユーザーは新しい認証 URL を含める必要があります。 詳細については、[StorSimple のネットワーク アラート](storsimple-8000-manage-alerts.md#networking-alerts)に関するページをご覧ください。

* **新しいバージョンの StorSimple Snapshot Manager** - StorSimple Snapshot Manager の新しいバージョンは、Update 5 と共にリリースされます。 このバージョンに更新することをお勧めします。 このバージョンは、Update 3 以降を実行しているすべての StorSimple デバイスと互換性があります。 詳細については、「[Deploy StorSimple Snapshot Manager (StorSimple Snapshot Manager のデプロイ)](storsimple-snapshot-manager-deployment.md)」をご覧ください。


## <a name="issues-fixed-in-update-5"></a>Update 5 で修正された問題

次の表は、Update 5 で修正された問題の概要を示しています。

| いいえ | 機能 | 問題 | 物理デバイスへの適用 | 仮想デバイスへの適用 |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell のリモート処理 |以前のリリースでは、Windows PowerShell 経由で StorSimple Cloud Appliance へのリモート接続の確立を試行しているときに、ユーザーはエラーを受信しました。 このリリースでは、この問題の根本原因が特定され、問題が修正されました。 |いいえ |はい |
| 2 |帯域幅テンプレート |以前のリリースでは、帯域幅のテンプレートに問題があり、デバイスでの構成よりも少ない帯域幅になっていました。 この問題は今回のリリースで解決されました。 |あり |あり |
| 3 |フェールオーバー |以前のリリースでは、ボリューム数の多いデバイスが Update 4 を実行している別のデバイスに対してフェールオーバーした場合に、アクセス制御レコードを適用しようとすると、プロセスが失敗していました。 この問題は今回のリリースで修正されました。 |はい |あり |



## <a name="known-issues-in-update-5-from-previous-releases"></a>以前のリリースから存在する Update 5 の既知の問題

Update 5 では、新しい問題は見つかっていません。 以前のリリースから Update 5 に持ち越された問題の一覧については、[Update 3 のリリース ノート](storsimple-update3-release-notes.md#known-issues-in-update-3)を参照してください。

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Update 5 のシリアル接続 SCSI (SAS) コントローラーおよびファームウェアの更新プログラム

このリリースには、SAS コントローラー、LSI ドライバー、ファームウェアの更新プログラムが含まれています。 これらの更新プログラムをインストールする方法の詳細については、StorSimple デバイスへの [Update 5 のインストール](storsimple-8000-install-update-5.md)に関する記事をご覧ください。

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Update 5 における StorSimple Cloud Appliance の更新プログラム

この更新プログラムはStorSimple Cloud Appliance (仮想デバイスとも呼ばれます) には適用できません。 新しいクラウド アプライアンスは、Update 5 のイメージを使用して作成する必要があります。 StorSimple Cloud Appliance の作成方法については、[StorSimple Cloud Appliance のデプロイと管理](storsimple-8000-cloud-appliance-u2.md)に関する記事をご覧ください。

## <a name="next-step"></a>次のステップ

StorSimple デバイスに [Update 5 をインストールする](storsimple-8000-install-update-5.md)方法を確認します。


