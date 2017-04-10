---
title: "Azure クラシック ポータルへの DPM ワークロードのバックアップ | Microsoft Docs"
description: "Azure Backup サービスを使用した DPM サーバーのバックアップの概要"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "System Center Data Protection Manager, Data Protection Manager, DPM バックアップ"
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: nkolli;giridham;markgal
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 9d1e68b0e73c60542de566c32c92caf0b3c4630c
ms.lasthandoff: 03/14/2017


---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>DPM を使用して Azure へのワークロードをバックアップするための準備
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (クラシック)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (クラシック)](backup-azure-dpm-introduction-classic.md)
>
>

この記事では、Microsoft Azure Backup を使用して System Center Data Protection Manager (DPM) サーバーとワークロードを保護する方法について概説します。 この記事を読むと次のことが理解できます。

* Azure DPM サーバーのバックアップの仕組み
* スムーズなバックアップ操作を実現するための前提条件
* 一般的に発生するエラーとその対処法
* サポートされるシナリオ

System Center DPM は、ファイルとアプリケーション データをバックアップします。 DPM にバックアップされるデータは、テープやディスクに保存することも、Microsoft Azure Backup を使って Azure にバックアップすることもできます。 DPM は、Azure Backup と次のように対話します。

* **物理サーバーまたはオンプレミス仮想マシンとしてデプロイされている DPM** — DPM が物理サーバーまたはオンプレミス Hyper-V 仮想マシンとしてデプロイされている場合、データはディスクやテープだけでなく、Azure Backup コンテナーにバックアップすることができます。
* **Azure の仮想マシンとしてデプロイされている DPM** — System Center 2012 R2 Update 3 以降、DPM は Azure 仮想マシンとしてデプロイすることができます。 DPM が Azure 仮想マシンとしてデプロイされている場合、DPM Azure 仮想マシンにアタッチされている Azure ディスクにデータをバックアップすることができます。またはデータを Azure Backup コンテナーにバックアップして、データ ストレージをオフロードすることができます。

## <a name="why-backup-your-dpm-servers"></a>DPM サーバーをバックアップする理由
DPM サーバーのバックアップに Azure Backup を使用するビジネス上のメリットは、次のとおりです。

* オンプレミス DPM デプロイメントでは、Azure バックアップはテープへの長期的なデプロイの代替手段として使用できます。
* Azure の DPM デプロイメントでは、Azure Backup を使用して Azure ディスクからストレージをオフロードし、Azure Backup に古いデータを、ディスクに新しいデータを格納してスケールアップすることができます。

## <a name="how-does-dpm-server-backup-work"></a>DPM サーバーのバックアップ動作のしくみ
仮想マシンをバックアップするには、まずデータの特定の時点のスナップショットが必要です。 Azure Backup サービスは、スケジュールされた時刻にバックアップ ジョブを開始し、バックアップ拡張機能をトリガーしてスナップショットを作成します。 バックアップ拡張機能は、インゲスト VSS サービスと連携して一貫性を実現し、一貫性に達すると、Azure ストレージ サービスの BLOB スナップショット API を呼び出します。 これにより、仮想マシンをシャット ダウンすることなく、一貫性のあるディスクのスナップショットが作成されます。

スナップショットが作成されると、データは Azure Backup サービスによってバックアップ コンテナーに転送されます。 このサービスは、最新のバックアップから変更されたブロックのみを識別して転送することで、バックアップ ストレージとネットワークの効率を高めます。 データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。 この回復ポイントは、Azure クラシック ポータルで確認できます。

> [!NOTE]
> Linux 仮想マシンでは、ファイル整合性のバックアップのみが可能です。
>
>

## <a name="prerequisites"></a>前提条件
DPM データをバックアップするために Azure Backup を準備するには、次のようにします。

1. **バックアップ コンテナーの作成**

  > [!IMPORTANT]
  > 2017 年 3 月以降、クラシック ポータルを使用してバックアップ コンテナーを作成することはできなくなります。 既にあるバックアップ コンテナーは引き続きサポートされ、[Azure PowerShell を使用してバックアップ コンテナーを作成](./backup-client-automation-classic.md#create-a-backup-vault)することが可能です。 ただし将来的な機能強化は Recovery Services コンテナーに限定されるため、Microsoft では、すべてのデプロイに関して Recovery Services コンテナーを作成することを推奨しています。

2. **コンテナー資格情報のダウンロード** — Azure Backup で、コンテナーに対して作成した管理証明書をダウンロードします。
3. **Azure Backup エージェントのインストールおよびサーバーの登録** — Azure Backup から、各 DPM サーバーにエージェントをインストールし、バックアップ コンテナーに DPM サーバーを登録します。

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>要件 (および制限)
* DPM は、物理サーバーとして、または System Center 2012 SP1 か System Center 2012 R2 にインストールされている Hyper-V 仮想マシンとして実行することができます。 さらに、DPM は、System Center 2012 R2 (少なくとも DPM 2012 R2 更新プログラム ロールアップ 3 が適用されているもの) で実行する Azure 仮想マシンとして、または System Center 2012 R2 (少なくとも更新プログラム ロールアップ 5 が適用されているもの) で実行する VMWare の Windows 仮想マシンとして実行することもできます。
* DPM を System Center 2012 SP1 で実行する場合は、System Center Data Protection Manager SP1 用の更新プログラム ロールアップ 2 をインストールする必要があります。 この手順は、Azure Backup エージェントをインストールする前に実行する必要があります。
* DPM サーバーには、Windows PowerShell および .Net Framework 4.5 がインストール済みである必要があります。
* DPM は、ほとんどのワークロードを Azure Backup にバックアップできます。 サポート対象の詳細な一覧については、次に示す Azure Backup サポートの項目を参照してください。
* Azure Backup に格納されているデータは、"テープにコピー" オプションでは回復できません。
* Azure Backup 機能が有効になっている Azure アカウントを使用する必要があります。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 [Azure Backup の料金](https://azure.microsoft.com/pricing/details/backup/)を参照してください。
* Azure Backup を使用するには、バックアップ対象のサーバーに Azure Backup エージェントがインストールされていることが必要です。 各サーバーをローカル ストレージとして使用するには、バックアップするデータのサイズの 10% 以上の空き領域が必要です。 たとえば、100 GB のデータをバックアップするには、スクラッチ場所に少なくとも 10 GB の空き領域が必要です。 最小要件は 10% ですが、キャッシュの場所には 15% のローカル ストレージ空き領域を使用することが推奨されます。
* データは、Azure コンテナー ストレージに格納されます。 Azure Backup コンテナーにバックアップできるデータ量に制限はありませんが、データ ソース (仮想マシンやデータベースなど) のサイズは 54,400 GB を超えないようにする必要があります。

Azure へのバックアップがサポートされているファイルの種類は、次のとおりです。

* 暗号化ファイル (完全バックアップのみ)
* 圧縮ファイル (増分バックアップがサポートされる)
* スパース ファイル (増分バックアップがサポートされる)
* 圧縮されたスパース ファイル (スパースとして処理)

次のものはサポートされていません。

* 大文字と小文字を区別するファイル システムのサーバーはサポートされません。
* ハード リンク (スキップされる)
* 再解析ポイント (スキップされる)
* 暗号化されている圧縮ファイル (スキップされる)
* 暗号化されているスパース ファイル (スキップされる)
* 圧縮ストリーム
* スパース ストリーム

> [!NOTE]
> System Center 2012 DPM SP1 以降では、Microsoft Azure Backup を使用して、DPM で保護されているワークロードを Azure にバックアップすることができます。
>
>

