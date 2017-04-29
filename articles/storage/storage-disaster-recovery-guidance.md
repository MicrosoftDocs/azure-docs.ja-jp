---
title: "Azure Storage の停止が発生した場合の対処方法 | Microsoft Docs"
description: "Azure Storage の停止が発生した場合の対処方法"
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 64650bf7baf46b0f5473deb1a9b4ec329979d153
ms.openlocfilehash: 0fc78521abb0fce2a38b14d1411dad42b3580df2
ms.lasthandoff: 01/21/2017


---

# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Azure Storage の停止が発生した場合の対処方法
Microsoft では、サービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス停止が 1 つまたは複数のリージョンで発生することがあります。 こうした状況はほとんど発生しませんが、発生した場合は、次のガイダンスに従って対応してください。

## <a name="how-to-prepare"></a>準備する方法
すべての顧客が独自の障害復旧計画を準備することが重要です。 ストレージの停止から復旧し、アプリケーションをアクティブ化して、機能している状態に戻すには、通常、運用担当者の操作と自動処理の両方が必要です。 障害復旧計画を作成するには、次の Azure ドキュメントをご覧ください。

* [Azure アプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure の回復性技術ガイダンス](../resiliency/resiliency-technical-guidance.md)
* [Azure Site Recovery サービス](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage のレプリケーション](storage-redundancy.md)
* [Azure Backup サービス](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>検出する方法
Azure サービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)をサブスクライブして確認することをお勧めします。

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Storage の停止が発生した場合の対処方法
1 つ以上のリージョンで 1 つ以上の Storage サービスが一時的に使用できない場合は、2 つのオプションがあります。 すぐにデータにアクセスする必要がある場合は、オプション 2 を検討してください。

### <a name="option-1-wait-for-recovery"></a>オプション 1: 復旧を待つ
この場合、ユーザーによる操作は必要ありません。 Azure サービスを利用できるようにするために鋭意取り組んでいます。 サービスの状態は [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で監視できます。

### <a name="option-2-copy-data-from-secondary"></a>オプション 2: セカンダリ リージョンからデータをコピーする
Storage アカウントに対して [読み取りアクセス地理冗長ストレージ (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (推奨) を選択した場合は、セカンダリ リージョンからデータに読み取りアクセスできます。 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md)、[Azure Data Movement Library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) などのツールを使用して、セカンダリ リージョンから、影響を受けていないリージョンの別の Storage アカウントにデータをコピーし、読み取りと書き込みの両方の可用性について、アプリケーションがその Storage アカウントを指すように指定します。

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Storage のフェールオーバーが発生した場合
[地理冗長ストレージ (GRS)](storage-redundancy.md#geo-redundant-storage) または[読み取りアクセス地理冗長ストレージ (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (推奨) を選択した場合、Azure Storage では 2 つのリージョン (プライマリおよびセカンダリ) でデータの持続性が維持されます。 この両方のリージョンでは、データのレプリカが常に複数保持されています。

地域的な災害がプライマリ リージョンに影響する場合、Microsoft では、まず、そのリージョン内のサービスを復元しようとします。 災害の性質とその影響によっては、まれではありますが、プライマリ リージョンを復元できないことがあります。 その場合は 地理フェールオーバーを実行します。 リージョン間のデータ レプリケーションは非同期プロセスのため、遅延が発生することがあり、セカンダリ リージョンにレプリケートされていない変更は失われる可能性があります。 レプリケーション ステータスの詳細情報は、 [ストレージ アカウントの "最終同期時刻"](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) で確認することができます。

ストレージ地理フェールオーバーに関する注意事項をいくつか次に示します。

* ストレージ地理フェールオーバーは、Azure Storage チームによってのみトリガーされます。顧客の操作は不要です。
* BLOB、テーブル、キュー、およびファイルに対する既存のストレージ サービス エンドポイントは、フェールオーバー後も同じままです。プライマリ リージョンからセカンダリ リージョンに切り替えるには、DNS エントリを更新する必要があります。
* 地理フェールオーバー前と地理フェールオーバー中は、障害の影響により Storage アカウントに書き込みアクセスできません。ただし、Storage アカウントが RA-GRS として構成されている場合、セカンダリ リージョンからの読み取りは引き続き可能です。
* 地理フェールオーバーが完了し、DNS の変更が反映されたら、Storage アカウントに再度読み取り/書き込みアクセスできるようになります。ポイント先は、以前のセカンダリ エンドポイントです。 
* Storage アカウントに GRS または RA-GRS を構成した場合は、書き込みアクセス権があることに注意してください。 
* 詳細情報は、 [Storage アカウントの "最終地理フェールオーバー時刻"](https://msdn.microsoft.com/library/azure/ee460802.aspx) で確認できます。
* フェールオーバー後、Storage アカウントは完全に機能しますが、"低下" 状態になります。実際は、地理レプリケーションを利用できないスタンドアロン リージョンでホストされているためです。 このリスクを軽減するために、Microsoft では、元のプライマリ リージョンを復元してから、地理フェールバックを実行し、元の状態を復元します。 元のプライマリ リージョンが復旧できない場合は、別のセカンダリ リージョンを割り当てます。
  Azure Storage 地理レプリケーションのインフラストラクチャの詳細については、[冗長オプションと RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) に関する Storage チーム ブログの記事をご覧ください。

## <a name="best-practices-for-protecting-your-data"></a>データ保護のベスト プラクティス
ストレージ データを定期的にバックアップするための推奨アプローチがいくつかあります。

* VM ディスク - [Azure Backup サービス](https://azure.microsoft.com/services/backup/) を使用して、Azure Virtual Machines で使用される VM ディスクをバックアップします。
* ブロック BLOB - 各ブロック BLOB の[スナップショット](https://msdn.microsoft.com/library/azure/hh488361.aspx)を作成するか、[AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md)、または [Azure Data Movement Library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) を使用して、他のリージョンの別の Storage アカウントに BLOB をコピーします。
* テーブル - [AzCopy](storage-use-azcopy.md) を使用して、テーブル データを、他のリージョンの別の Storage アカウントにエクスポートします。
* ファイル - [AzCopy](storage-use-azcopy.md) または [Azure PowerShell](storage-powershell-guide-full.md) を使用して、他のリージョンの別の Storage ストレージ アカウントにファイルをコピーします。

RA-GRS の機能を最大限に活用したアプリケーションを設計する方法の詳細については、「[RA-GRS ストレージを使用した高可用性アプリケーションの設計](storage-designing-ha-apps-with-ragrs.md)」を参照してください。


