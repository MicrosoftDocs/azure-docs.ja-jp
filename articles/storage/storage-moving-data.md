---
title: "Azure のクラウド ストレージ間で大量のデータを移動する | Microsoft Docs"
description: "Azure Storage との間でデータを移動するためのさまざまな方法の概要を説明します。"
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
translationtype: Human Translation
ms.sourcegitcommit: 245fb30a64c8a19f6ea0073d533419e9c3628cb5
ms.openlocfilehash: 538a43e549f47709616dd93e7eab9c8cb7d99dc6
ms.lasthandoff: 02/10/2017


---
# <a name="moving-data-to-and-from-azure-storage"></a>Azure Storage との間でのデータの移動
オンプレミスのデータを Azure Storage に (またはその逆に) 移動する場合は、さまざまな方法があります。 最適な方法は、シナリオによって異なります。 この記事では、さまざまなシナリオとそれぞれに適した手段の概要を説明します。

## <a name="building-applications"></a>アプリケーションの構築
アプリケーションを構築する場合は、REST API またはいずれかのクライアント ライブラリを使用するのが、Azure Storage との間でデータを移動する優れた方法です。

Azure Storage では、.NET、iOS、Java、Android、Universal Windows Platform (UWP)、Xamarin、C++、Node.JS、PHP、Ruby、Python 用の充実したクライアント ライブラリが用意されています。 クライアント ライブラリは、再試行ロジック、ログ、並列アップロードといった高度な機能を提供します。 また、REST API を直接使用して開発することもでき、HTTP/HTTPS 要求を行うどの言語からでも呼び出すことができます。

詳細については、「 [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md) 」を参照してください。

さらに、Azure との間で相互に高パフォーマンスのデータ コピーを行うことができるように設計されたライブラリである、 [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) も用意されています。 詳細については、Data Movement Library の [ドキュメント](https://github.com/Azure/azure-storage-net-data-movement) を参照してください。 

## <a name="quickly-viewinginteracting-with-your-data"></a>データのすばやい表示/操作
Azure Storage のデータを簡単に表示するだけでなく、データのアップロードやダウンロードも行いたい場合は、Azure ストレージ エクスプローラーを使用できます。

詳細については、 [Azure ストレージ エクスプローラー](storage-explorers.md) の一覧を確認してください。

## <a name="system-administration"></a>システム管理
コマンドライン ユーティリティが必要な場合、またはコマンドライン ユーティリティの方が使い慣れている場合は (システム管理者など)、次のような選択肢があります。

### <a name="azcopy"></a>AzCopy
AzCopy は、Azure Storage との間で高パフォーマンスのデータ コピーを行うように設計された Windows コマンドライン ユーティリティです。 同じストレージ アカウント内または異なるストレージ アカウント間でデータをコピーすることもできます。

詳細については、「 [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md) 」を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell は、Azure でサービスを管理するためのコマンドレットを提供するモジュールです。 タスク ベースのコマンドライン シェルであり、特にシステム管理用に設計されたスクリプト言語です。

詳細については、「 [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md) 」を参照してください。

### <a name="azure-cli"></a>Azure CLI
Azure CLI は、Azure サービスで使用できるオープン ソース、クロスプラットフォームのコマンド群です。 Azure CLI は、Windows、OSX、Linux で使用できます。

詳細については、「 [Azure Storage での Azure CLI の使用](storage-azure-cli.md) 」を参照してください。

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>低速ネットワークでの大量のデータの移動
大量のデータの移動に関する最も大きな課題の&1; つは、転送時間です。 ネットワーク コストやコードの作成を心配することなく Azure Storage との間でデータを移動したい場合は、Azure Import/Export が適切なソリューションです。

詳細については、 [Azure Import/Export](storage-import-export-service.md) に関する記事を参照してください。

## <a name="backing-up-your-data"></a>データのバックアップ
Azure Storage にデータをバックアップするだけの場合は、Azure Backup が適しています。 これは、オンプレミスのデータと Azure VM をバックアップするための強力なソリューションです。

詳細については、 [Azure Backup](../backup/backup-introduction-to-azure-backup.md) に関する記事を参照してください。

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>オンプレミスおよびクラウドからのデータへのアクセス
オンプレミスで、またはクラウドからデータにアクセスするためのソリューションが必要な場合は、Azure のハイブリッド クラウド ストレージ ソリューションである StorSimple を使用できます。 このソリューションは、頻繁に使用されるデータを SSD にインテリジェントに格納する物理 StorSimple デバイスと、Azure Storage 上の非アクティブ/バックアップ/アーカイブ データで構成されます。

詳細については、 [StorSimple](../storsimple/storsimple-overview.md) に関する記事を参照してください。

## <a name="recovering-your-data"></a>データの復旧
オンプレミスのワークロードとアプリケーションがある場合、障害が発生してもビジネスを継続できるソリューションが必要です。 Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を処理します。 レプリケートされたデータは Azure Storage に格納されるので、オンサイトのセカンダリ データセンターは必要ありません。

詳細については、 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) に関する記事を参照してください。
### <a name="moving-data-faq"></a>データの移動に関してよく寄せられる質問:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>VHD をコピーせずにリージョン間で移行できますか?
リージョン間で VHD をコピーするには、各リージョンのストレージ アカウント間でデータをコピーする方法しかありません。 これには AZCopy を使用します。 詳細については、「AzCopy コマンド ライン ユーティリティを使用してデータを転送する」を参照してください。 データ容量が非常に大きい場合には、Azure Import/Export を使用することもできます。 詳細については、 [Azure Import/Export](https://docs.microsoft.com/en-us/azure/storage/storage-import-export-service) に関する記事を参照してください。

