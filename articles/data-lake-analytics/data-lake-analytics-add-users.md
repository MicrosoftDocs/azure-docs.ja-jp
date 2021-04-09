---
title: Azure Data Lake Analytics アカウントにユーザーを追加する
description: ユーザーの追加ウィザードと Azure PowerShell を利用し、Data Lake Analytics アカウントにユーザーを正しく追加する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020825"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Azure portal でユーザーを追加する

## <a name="start-the-add-user-wizard"></a>ユーザーの追加ウィザードを開始する
1. [https://portal.azure.com](https://portal.azure.com ) で Azure Data Lake Analytics を開きます。
2. **[ユーザーの追加ウィザード]** をクリックします。
3. **[ユーザーを選択する]** 手順で、追加するユーザーを探します。 **[選択]** をクリックします。
4. **[ロールを選択する]** 手順で、 **[Data Lake Analytics の開発者]** を選択します。 このロールには、U-SQL ジョブの送信/監視/管理に必要な最小限のアクセス許可が割り当てられています。 Azure サービスの管理を目的としていないグループの場合は、このロールに割り当てます。
5. **[カタログ アクセス許可の選択]** 手順で、ユーザーがアクセスする必要がある追加データベースがあれば選択します。 ジョブを送信するには、"master" と呼ばれるデフォルトの静的データベースに対する [読み取り] と [書き込み] のアクセス権が必要です。 終了したら **[OK]** をクリックします。
6. **[選択したアクセス許可の割り当て]** という最後の手順で、ウィザードが行う変更内容を確認します。 **[OK]** をクリックします。


## <a name="configure-acls-for-data-folders"></a>データ フォルダーの ACL を構成する
必要に応じて、入力データと出力データが保存されているフォルダーに対して "R-X" または "RWX" を付与します。


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>必要に応じて、ユーザーを Azure Data Lake Storage Gen1 ロールの **閲覧者** ロールに追加します。
1.  Azure Data Lake Storage Gen1 アカウントを見つけます。
2.  **[ユーザー]** をクリックします。
3. **[追加]** をクリックします。
4.  このグループを割り当てる Azure ロールを選択します。
5.  閲覧者ロールを割り当てます。 このロールには、ADLSGen1 に格納されているデータの参照/管理に必要な最小限のアクセス許可が割り当てられています。 Azure サービスの管理を目的としていないグループの場合は、このロールに割り当てます。
6.  グループの名前を入力します。
7.  **[OK]** をクリックします。

## <a name="adding-a-user-using-powershell"></a>PowerShell を使用してユーザーを追加する

1. このガイドの [Azure PowerShell のインストールと構成の方法](/powershell/azure/)に関するページに記載されている手順に従います。
2. [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell スクリプトをダウンロードします。
3. PowerShell スクリプトを実行します。 

ジョブの送信、新しいジョブ メタデータの表示、古いメタデータの表示にユーザーがアクセスできるようにするサンプル コマンドは次のとおりです。

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-powershell.md)
