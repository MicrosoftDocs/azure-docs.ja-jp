---
title: ログ ディスクを Ultra Disk に移行する
description: Azure 仮想マシン (VM) のログ ディスク上の SQL Server を Azure Ultra Disk に移行してハイ パフォーマンスと低待機時間を利用する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 42738ff71432284a156d0dfbb1f6cf160cbf4032
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653251"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>ログ ディスクを Ultra Disk に移行する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra Disk では、Azure 仮想マシン (VM) 上の SQL Server 用に高スループット、高 IOPS、一貫して低待機時間のディスク ストレージを提供します。 

この記事では、Ultra Disk で提供されるパフォーマンスの利点を利用するために、ログ ディスクを Ultra SSD に移行する方法について説明します。 

## <a name="back-up-database"></a>データベースをバックアップする

データベースの[完全バックアップ](backup-restore.md)を完了します。 

## <a name="attach-disk"></a>ディスクをアタッチする

VM で Ultra Disk の互換性を有効にした後、Ultra SSD を仮想マシンにアタッチします。 

Ultra Disk は、VM のサイズとリージョンのサブセットでサポートされます。 続行する前に、VM が、Ultra Disk をサポートするリージョン、ゾーン、サイズであることを検証します。 [VM のサイズとリージョンを確認し、検証する](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability)には、Azure CLI または PowerShell を使用します。 

### <a name="enable-compatibility"></a>互換性を有効にする

互換性を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/)でご使用の仮想マシンに移動します。 
1. 仮想マシンを停止または割り当て解除します。 
1. **[設定]** で **[ディスク]** を選択し、 **[追加設定]** を選択します。 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Azure portal の [設定] で [ディスク] の追加設定を選択する":::

1. **[Ultra Disk の互換性を有効にする]** に対して **[はい]** を選択します。 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Azure portal の [設定] で [ディスク] の追加設定を選択する":::

1. **[保存]** を選択します。 



### <a name="attach-disk"></a>ディスクをアタッチする

Azure portal を使用して、Ultra Disk を仮想マシンにアタッチします。 詳細については、[Ultra Disk のアタッチ](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk-using-the-azure-portal)に関するページを参照してください

ディスクをアタッチしたら、Azure portal を使用して VM をもう一度起動します。 



## <a name="format-disk"></a>ディスクをフォーマットする

仮想マシンに接続して、Ultra Disk をフォーマットします。  

Ultra Disk をフォーマットするには、次の手順に従います。

1. リモート デスクトップ プロトコル (RDP) を使用して VM に接続します。
1. [ディスクの管理](/windows-server/storage/disk-management/overview-of-disk-management)を使用して、新しくアタッチした Ultra Disk をフォーマットし、パーティション分割します。 


## <a name="use-disk-for-log"></a>ディスクをログに使用する

新しいログ ドライブを使用するように SQL Server を構成します。 このためには、Transact-SQL (T-SQL) または SQL Server Management Studio (SSMS) を使用します。 SQL Server サービス アカウントに使用するアカウントには、新しいログ ファイルの場所に対するフル コントロールが必要です。 

### <a name="configure-permissions"></a>権限の構成

1. SQL Server で使用されるサービス アカウントを検証します。 このためには、SQL Server 構成マネージャーまたは Services.msc を使用します。
1. 新しいディスクに移動します。 
1. ログ ファイルに使用する 1 つまたは複数のフォルダーを作成します。 
1. フォルダーを右クリックし、 **[プロパティ]** をクリックします。
1. **[セキュリティ]** タブで、SQL Server サービス アカウントにフル コントロールのアクセスを許可します。 
1. **[OK]** を選択して設定を保存します。 
1. SQL データを保持する予定のすべてのルートレベルのフォルダーについて、これを繰り返します。 

### <a name="use-new-log-drive"></a>新しいログ ドライブを使用する 

アクセス許可を付与した後、Transact-SQL (T-SQL) または SQL Server Management Studio (SSMS) を使用して、データベースをデタッチし、既存のログ ファイルを新しい場所に移動します。

   > [!CAUTION]
   > データベースをデタッチすると、オフラインになり、接続が閉じられ、送信中のトランザクションがロールバックされます。 注意してダウンタイム メンテナンス期間に進みます。 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

T-SQL を使用して、既存のファイルを新しい場所に移動します。

1. SQL Server Management Studio でデータベースに接続し、 **[新しいクエリ]** ウィンドウを開きます。 
1. 既存のファイルと場所を取得します。

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. データベースをデタッチします。 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. ファイル エクスプローラーを使用して、ログ ファイルを Ultra Disk 上の新しい場所に移動します。 

1. 新しいファイルの場所を指定して、データベースをアタッチします。 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

この時点で、データベースはオンラインになり、新しい場所にログインします。 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

SSMS を使用して既存のファイルを新しい場所に移動します。

1. SQL Server Management Studio (SSMS) でデータベースに接続します。 
1. データベースを右クリックして、 **[プロパティ]** 、 **[ファイル]** の順に選択します。 
1. 既存のファイルのパスをメモします。 
1. **[OK]** を選択してダイアログ ボックスを閉じます。 
1. データベースを右クリックして、 **[タスク]**  >  **[デタッチ]** を選択します。 
1. ウィザードに従って、データベースをデタッチします。 
1. ファイル エクスプローラーを使用して、ログ ファイルを新しい場所に手動で移動します。
1. SQL Server Management Studio でデータベースをアタッチします。
   1. **オブジェクト エクスプローラー**で **[データベース]** を右クリックして、 **[データベースのアタッチ]** を選択します。 
   1. ダイアログ ボックスを使用して、新しい場所にあるログ ファイルを含めて各ファイルを追加します。 
   1. **[OK]** を選択し、データベースをアタッチします。 

この時点で、データベースはオンラインになり、新しい場所にログインします。

---


## <a name="next-steps"></a>次のステップ

パフォーマンスを向上するための追加設定については、[パフォーマンスのベスト プラクティス](performance-guidelines-best-practices.md)に関するページを参照してください。 

Azure Virtual Machines における SQL Server の概要については、次の記事を参照してください。

- [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM における SQL Server の概要](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
