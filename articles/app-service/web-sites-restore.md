---
title: アプリの復元 - Azure App Service
description: アプリをバックアップから復元する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721463"
---
# <a name="restore-an-app-in-azure"></a>Azure でのアプリの復元
この記事では、[Azure App Service](../app-service/overview.md) でアプリをバックアップから復元する方法を示します (バックアップについては「[Azure App Service での Web アプリのバックアップ](manage-backup.md)」を参照してください)。 リンクされたデータベースをオンデマンドで使用してアプリを以前の状態に戻したり、元のアプリのいずれかのバックアップに基づいて新しいアプリを作成したりできます。 Azure App Service では、次のデータベースのバックアップと復元がサポートされます。
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [アプリ内 MySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

バックアップからの復元は、**Standard** レベルか **Premium** レベルで実行されているアプリでのみ利用できます。 アプリのスケーリングについて詳しくは、「 [Azure App Service の Web アプリをスケーリングする](web-sites-scale.md)」をご覧ください。 **Premium** レベルでは、**Standard** レベルよりも多くの回数の日次バックアップが可能です。

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>既存のバックアップからのアプリの復元
1. Azure Portal にあるアプリの **[設定]** ページで、**[バックアップ]** をクリックして **[バックアップ]** ページを表示します。 続けて **[復元]** をクリックしてください。
   
    ![今すぐ復元の選択][ChooseRestoreNow]
2. **[復元]** ページで、最初にバックアップ ソースを選択します。
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    **[アプリのバックアップ]** オプションには、現在のアプリの既存のバックアップがすべて表示されるので、バックアップを簡単に選択することができます。
    **[ストレージ]** オプションを使用すると、サブスクリプションに含まれる既存の Azure Storage アカウントとコンテナーから、任意のバックアップ ZIP ファイルを選択できます。
    別のアプリのバックアップを復元する場合は、 **[ストレージ]** オプションを使用します。
3. 次に、 **[復元先]** でアプリの復元先を指定します。
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > **[上書き]** を選択した場合、現在のアプリの既存のデータはすべて消去され上書きされます。 **[OK]** をクリックする前に、実行する操作内容が正しいことを確認します。
   > 
   > 
   
   > [!WARNING]
   > 復元中のデータベースに App Service がデータを書き込んだ場合、プライマリ キーの違反やデータの損失などの症状が生じることがあります。 データベースの復元を開始する前に、App Service を停止しておくことをお勧めします。
   > 
   > 
   
    **[既存のアプリ]** を選択して、アプリのバックアップを同じリソース グループ内の別のアプリに復元することができます。 このオプションを使用する前に、リソース グループ内に別のアプリを作成済みであり、データベース構成を、アプリのバックアップ内に定義されている構成にミラーリングしている必要があります。 コンテンツの復元先として**新しい**アプリを作成することもできます。

4. Click **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>ストレージ アカウントからバックアップをダウンロードまたは削除するには
1. Azure Portal のメインの **[参照]** ページで、**[ストレージ アカウント]** を選択します。 既存のストレージ アカウントの一覧が表示されます。
2. ダウンロードまたは削除するバックアップが含まれているストレージ アカウントを選択します。 そのストレージ アカウントのページが表示されます。
3. ストレージ アカウントのページで、必要なコンテナーを選択します。
   
    ![コンテナーの表示][ViewContainers]
4. ダウンロードまたは削除するバックアップ ファイルを選択します。
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 目的に応じて、**[ダウンロード]** または **[削除]** をクリックします。  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>復元操作の確認
アプリの復元操作が成功したかどうかについて詳細を確認するには、Azure Portal の **[アクティビティ ログ]** ページに移動します。  
 

下方向へスクロールし、目的の復元操作が見つかったらクリックして選択します。

詳細ページに、復元操作に関連する利用可能な情報が表示されます。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、バックアップ管理をスクリプトで自動化できます。

サンプルについては、以下を参照してください。

- [Azure CLI のサンプル](samples-cli.md)
- [Azure PowerShell のサンプル](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
