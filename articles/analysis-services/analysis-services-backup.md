---
title: Azure Analysis Services データベースのバックアップと復元 | Microsoft Docs
description: Azure Analysis Services データベースのバックアップと復元の方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31e8e65b382a3a6bcad2998a0babdf9605dc4968
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539107"
---
# <a name="backup-and-restore"></a>バックアップと復元

Azure Analysis Services の表形式モデル データベースのバックアップは、オンプレミスの Analysis Services の場合とほぼ同じです。 主な違いは、バックアップ ファイルの保存場所です。 バックアップ ファイルは、[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md)のコンテナーに保存する必要があります。 既存のストレージ アカウントとコンテナーを使用できるほか、ご利用のサーバーのストレージを構成する際にストレージ アカウントとコンテナーを作成することもできます。

> [!NOTE]
> ストレージ アカウントを作成すると、新しい課金対象サービスを使用することになる場合があります。 詳細については、「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。
> 
> 

バックアップは、.abf という拡張子で保存されます。 インメモリの表形式モデルの場合、モデル データとメタデータの両方が保存されます。 DirectQuery 表形式モデルの場合、モデルのメタデータのみが保存されます。 バックアップは、オプションの選択によって、圧縮したり暗号化したりすることができます。


## <a name="configure-storage-settings"></a>ストレージ設定の構成
バックアップを実行する前に、サーバーのストレージ設定を構成する必要があります。


### <a name="to-configure-storage-settings"></a>ストレージ設定を構成するには
1.  Azure Portal の **[設定]** で、**[バックアップ]** をクリックします。

    ![[設定] の [バックアップ]](./media/analysis-services-backup/aas-backup-backups.png)

2.  **[有効]** をクリックし、**[ストレージの設定]** をクリックします。

    ![有効化](./media/analysis-services-backup/aas-backup-enable.png)

3. 既存のストレージ アカウントを選択するか、新しいアカウントを作成します。

4. コンテナーを選択するか、新たに作成します。

    ![コンテナーの選択](./media/analysis-services-backup/aas-backup-container.png)

5. バックアップ設定を保存します。

    ![バックアップ設定の保存](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>バックアップ

### <a name="to-backup-by-using-ssms"></a>SSMS を使用してバックアップするには

1. SSMS でデータベースを右クリックし、**[バックアップ]** を選択します。

2. **[データベースのバックアップ]** > **[バックアップ ファイル]** の順に移動し、**[参照]** をクリックします。

3. **[ファイル名を付けて保存]** ダイアログでフォルダーのパスを確認し、バックアップ ファイルの名前を入力します。 

4. **[ データベースのバックアップ]** ダイアログで、オプションを選択します。

    **[ファイルの上書きを許可する]** - 同じ名前のバックアップ ファイルを上書きするには、このオプションを選択します。 このオプションを選択しなかった場合、保存しているファイルに、同じ場所に既に存在するファイルと同じ名前を付けることはできません。

    **[圧縮を適用する]** - バックアップ ファイルを圧縮するには、このオプションを選択します。 バックアップ ファイルを圧縮することでディスク領域が節約されますが、CPU 使用率が若干高くなります。 

    **[バックアップ ファイルを暗号化する]** - バックアップ ファイルを暗号化するには、このオプションを選択します。 このオプションを選択した場合、バックアップ ファイルをセキュリティで保護するためにユーザー指定のパスワードが必要になります。 このパスワードによって、復元操作以外の手段でバックアップ データが読み取られることを防ぐことができます。 バックアップを暗号化する場合は、パスワードを安全な場所に保管してください。

5. **[OK]** をクリックすると、バックアップ ファイルが作成されて保存されます。


### <a name="powershell"></a>PowerShell
[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) コマンドレットを使用します。

## <a name="restore"></a>復元
復元の際、バックアップ ファイルは、サーバー用に構成したストレージ アカウントにある必要があります。 バックアップ ファイルをオンプレミスの場所からストレージ アカウントに移動する必要がある場合は、[Microsoft Azure ストレージ エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)か、[AzCopy](../storage/common/storage-use-azcopy.md) コマンドライン ユーティリティを使用してください。 



> [!NOTE]
> オンプレミスのサーバーから復元する場合、モデルのロールからすべてのドメイン ユーザーを削除し、それらのユーザーを Azure Active Directory ユーザーとしてロールに追加し直す必要があります。
> 
> 

### <a name="to-restore-by-using-ssms"></a>SSMS を使って復元するには

1. SSMS でデータベースを右クリックし、**[復元]** を選択します。

2. **[データベースのバックアップ]** ダイアログの **[バックアップ ファイル]** で **[参照]** をクリックします。

3. **[データベース ファイルの検索]** ダイアログで、復元するファイルを選択します。

4. **[データベースの復元]** でデータベースを選択します。

5. オプションを指定します。 セキュリティ オプションは、バックアップ時に使用したバックアップ オプションと合わせる必要があります。


### <a name="powershell"></a>PowerShell

[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) コマンドレットを使用します。


## <a name="related-information"></a>関連情報

[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md)  
[高可用性](analysis-services-bcdr.md)     
[Azure Analysis Services を管理する](analysis-services-manage.md)
