---
title: Azure サービスのサーバーへのアクセスを許可せずに Azure SQL Database をインポートまたはエクスポートする
description: Azure サービスのサーバーへのアクセスを許可せずに Azure SQL Database をインポートまたはエクスポートする
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724171"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Azure サービスのサーバーへのアクセスを許可せずに Azure SQL Database をインポートまたはエクスポートする
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、サーバーで *[Azure サービスを許可する]* に *[オフ]* が設定されている場合に Azure SQL Database をインポートまたはエクスポートする方法について説明します。 このワークフローでは、インポートまたはエクスポート操作を行う SqlPackage を実行するために Azure 仮想マシンを使用します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-the-azure-virtual-machine"></a>Azure 仮想マシンを作成する

**[Azure へのデプロイ]** ボタンを選択して Azure 仮想マシンを作成します。

このテンプレートでは、修正プログラムが適用された最新バージョンを使用して、Windows バージョンのいくつかの異なるオプションを使用して単純な Windows 仮想マシンをデプロイできます。 これにより、A2 サイズの VM がリソース グループの場所にデプロイされ、その VM の完全修飾ドメイン名が返されます。
<br><br>

[![[Deploy to Azure]\(Azure にデプロイ\) というラベルが付けられたボタンが表示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

詳細については、[Windows VM の非常に単純なデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)に関するページを参照してください。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

次の手順は、リモート デスクトップ接続を使用して仮想マシンに接続する方法を示しています。

1. デプロイが完了したら、仮想マシン リソースに移動します。

   ![仮想マシンの [概要] ページ ([接続] ボタンを含む) を示すスクリーンショット。](./media/database-import-export-azure-services-off/vm.png)  

2. **[接続]** を選択します。

   リモート デスクトップ プロトコル ファイル (.rdp ファイル) のフォームが表示され、仮想マシンのパブリック IP アドレスとポート番号が示されます。

   ![RDP フォーム](./media/database-import-export-azure-services-off/rdp.png)  

3. **[RDP ファイルのダウンロード]** を選択します。

   > [!NOTE]
   > SSH を使用して VM に接続することもできます。

4. **[仮想マシンに接続する]** フォームを閉じます。
5. VM に接続するには、ダウンロードした RDP ファイルを開きます。
6. メッセージが表示されたら、 **[Connect]** を選択します。 Mac では、この[リモート デスクトップ クライアント](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)のような RDP クライアントを Mac App Store から入手する必要があります。

7. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、 **[OK]** を選択します。

8. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** を選択して接続処理を続行します。

## <a name="install-sqlpackage"></a>SqlPackage をインストールする

[最新バージョンの SqlPackage をダウンロードしてインストール](/sql/tools/sqlpackage-download)します。

詳細については、「[SqlPackage.exe](/sql/tools/sqlpackage)」を参照してください。

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>データベースへの VM アクセスを許可するためのファイアウォール規則を作成する

サーバーのファイアウォールに仮想マシンのパブリック IP アドレスを追加します。

次の手順では、仮想マシンのパブリック IP アドレスのためのサーバー レベルの IP ファイアウォール規則を作成し、仮想マシンからの接続を有効にします。

1. 左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページでデータベースを選択します。 データベースの概要ページが開き、完全修飾サーバー名 (**servername.database.windows.net** など) と、さらに構成するためのオプションが表示されます。

2. サーバーやそのデータベースに接続するときに使用するために、この完全修飾サーバー名をコピーします。

   ![サーバー名](./media/database-import-export-azure-services-off/server-name.png)

3. ツール バーで **[サーバー ファイアウォールの設定]** を選択します。 サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーレベルの IP ファイアウォール規則](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. ツールバーの **[クライアント IP の追加]** を選択して、サーバー レベルの新しい IP ファイアウォール規則に仮想マシンのパブリック IP アドレスを追加します。 サーバーレベルの IP ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

5. **[保存]** を選択します。 サーバー レベルの IP ファイアウォール規則が、サーバー上のポート 1433 を開いている仮想マシンのパブリック IP アドレスに対して作成されます。

6. **[ファイアウォール設定]** ページを閉じます。

## <a name="export-a-database-using-sqlpackage"></a>SqlPackage を使用してデータベースをエクスポートする

[SqlPackage](/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して Azure SQL Database をエクスポートするには、「[エクスポートのパラメーターおよびプロパティ](/sql/tools/sqlpackage#export-parameters-and-properties)」を参照してください。 SqlPackage ユーティリティには、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) と [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) が付属しています。または、最新バージョンの [SqlPackage](/sql/tools/sqlpackage-download) をダウンロードすることもできます。

ほとんどの運用環境でのスケールとパフォーマンスのために、SqlPackage ユーティリティを使用することをお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)」を参照してください。

この例は、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをエクスポートする方法を示しています。 環境に固有の値に置き換えてください。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>SqlPackage を使用してデータベースをインポートする

[SqlPackage](/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL Server データベースをインポートするには、「[インポート パラメーターとプロパティ](/sql/tools/sqlpackage#import-parameters-and-properties)」をご覧ください。 SqlPackage には、最新の [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) と [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) が含まれています。 また、最新バージョンの [SqlPackage](/sql/tools/sqlpackage-download) をダウンロードすることもできます。

スケールとパフォーマンスのために、ほとんどの運用環境では、Azure portal の使用ではなく SqlPackage の使用をお勧めします。 `BACPAC` ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)」をご覧ください。

次の SqlPackage コマンドでは、**AdventureWorks2017** データベースがローカル ストレージから Azure SQL Database にインポートされます。 **Premium** サービス層と **P6** サービス オブジェクトがある **myMigratedDatabase** という新しいデータベースが作成されます。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 企業のファイアウォールの外側から Azure SQL Database に接続するには、ファイアウォールのポート 1433 が開いている必要があります。

この例では、SqlPackage と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

エクスポートの速度は多くの要因 (データ シェープなど) によって異なるため、どのような速度が期待されるかを予測することはできません。 特に大規模なデータベースの場合は、SqlPackage にかなりの時間がかかることがあります。

最高のパフォーマンスを得るために、次の方法を試してみることができます。

1. データベース上で確実に他のどのワークロードも実行されないようにします。 他のどのワークロードも実行されないようにするには、エクスポートの前のコピーの作成が最適なソリューションである可能性があります。
2. エクスポート ワークロード (主に読み取り I/O) をより適切に処理するには、データベースのサービス レベル目標 (SLO) を上げます。 現在、データベースが GP_Gen5_4 である場合は、Business Critical レベルが読み取りワークロードに役立つことがあります。
3. 特に大規模なテーブルに対して、確実にクラスター化インデックスが存在するようにします。
4. ネットワークの制約を回避できるように、仮想マシン (VM) はデータベースと同じリージョンにある必要があります。
5. VM には、BLOB ストレージにアップロードする前の一時的な成果物を生成するための十分なサイズを持つ SSD が存在する必要があります。
6. VM は、特定のデータベースのための十分なコアおよびメモリ構成を備えている必要があります。

## <a name="store-the-imported-or-exported-bacpac-file"></a>インポートまたはエクスポートされた .BACPAC ファイルを格納する

.BACPAC ファイルは、[Azure BLOB](../../storage/blobs/storage-blobs-overview.md) または [Azure Files](../../storage/files/storage-files-introduction.md) に格納できます。

最高のパフォーマンスを実現するには、Azure Files を使用します。 SqlPackage はファイル システムで動作するため、Azure Files に直接アクセスできます。

コストを削減するには、Premium Azure ファイル共有よりコストの低い Azure BLOB を使用します。 ただし、それには、インポートまたはエクスポート操作の前に BLOB とローカル ファイル システムの間で [.BACPAC ファイル](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)をコピーする必要があります。 その結果、このプロセスにかかる時間は長くなります。

.BACPAC ファイルをアップロードまたはダウンロードするには、「[AzCopy と Blob Storage でデータを転送する](../../storage/common/storage-use-azcopy-v10.md#transfer-data)」および「[AzCopy とファイル ストレージでデータを転送する](../../storage/common/storage-use-azcopy-files.md)」 を参照してください。

環境によっては、[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../storage/common/storage-network-security.md)ことが必要になる場合があります

## <a name="next-steps"></a>次のステップ

- インポートした SQL Database に接続してそれを照会する方法については、「[クイック スタート:Azure SQL Database:SQL Server Management Studio を使ってデータに接続してクエリを行う方法](connect-query-ssms.md)に関する記事をご覧ください。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)」を参照してください。
- パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](migrate-to-database-from-sql-server.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](../../storage/blobs/security-recommendations.md)」をご覧ください。
