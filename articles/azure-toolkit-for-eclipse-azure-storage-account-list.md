---
title: "Azure ストレージ アカウントの一覧"
description: "Azure Toolkit for Eclipse を使用した、ストレージ アカウントの設定の管理"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: bbacfcd8-dbf5-4265-a930-59f508de5325
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9ef430dfaa43f9bc4294593c8abbdaf457ef07c8


---
# <a name="azure-storage-account-list"></a>Azure ストレージ アカウントの一覧
Azure ストレージ アカウントは、JDK、アプリケーション サーバー、および任意のコンポーネントのためだけでなく、キャッシュを使用する際に、状態を格納するために使用されるダウンロードの場所を提供します。 Eclipse では、Eclipse ワークスペース内のプロジェクトで使用可能な既知のストレージ アカウントの一覧が保持されます。 Eclipse で一覧の管理に使用される **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスを開くには、**[Window (ウィンドウ)]** をクリックして **[Preferences (ユーザー設定)]** をクリックし、**[Azure]** を展開して、**[Storage Accounts (ストレージ アカウント)]** をクリックします。

次に **[ストレージ アカウント]** ダイアログ ボックスを示します。

![][ic719496]

このダイアログ ボックスは、ストレージ アカウントを使用する次のようなダイアログ ボックスの **[アカウント]** リンクからも開くことができます。

* **[Server Configuration (サーバー構成)]** ダイアログ ボックスの **[JDK]** タブ。
* **[Server Configuration (サーバー構成)]** ダイアログ ボックスの **[Server (サーバー)]** タブ。
* **[Add Component]** ダイアログ ボックス。
* **[キャッシュ]** プロパティ ダイアログ ボックス。

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>発行設定ファイルを使用して、ストレージ アカウントをインポートするには
1. **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスで、**[Import from PUBLISH-SETTINGS file (PUBLISH-SETTINGS ファイルからインポート)]** をクリックします。
2. (ローカル コンピューターへ既に発行設定ファイルを保存している場合、この手順はスキップしてください。)**[Import Subscription Information (サブスクリプション情報のインポート)]** ダイアログ ボックスの **[Download PUBLISH-SETTINGS File (PUBLISH-SETTINGS ファイルのダウンロード)]** をクリックします。 まだ Azure アカウントにログインしていない場合は、ログインするように求められます。 その後、Azure 発行設定ファイルを保存するよう求めるメッセージが表示されます。 (結果としてログオン ページに表示される手順は無視することができます。それらは、Azure ポータルによって提供される、Visual Studio ユーザー向けの手順です。)このファイルは、ローカル コンピューターに保存してください。
3. 引き続き **[Import Subscription Information (サブスクリプション情報のインポート)]** ダイアログ ボックスで **[Browse (参照)]** ボタンをクリックし、前にローカルで保存した発行設定ファイルを選択して **[Open (開く)]** をクリックします。
4. **[OK]** をクリックして、**[Import Subscription Information (サブスクリプション情報のインポート)]** ダイアログ ボックスを閉じます。

## <a name="to-create-a-new-storage-account"></a>新しいストレージ アカウントを作成するには
1. **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスで、**[Add (追加)]** をクリックします。
2. **[Add Storage Account (ストレージ アカウントの追加)]** ダイアログ ボックスで、**[New (新規)]** をクリックします。
3. **[新規ストレージ アカウント]** ダイアログ ボックスで、次の値を指定します。
   * ストレージ アカウント名。
   * ストレージ アカウントの場所。
   * ストレージ アカウントの説明。
   * ストレージ アカウントが属しているサブスクリプション。
4. **[OK]** をクリックして、**[New Storage Account (新規ストレージ アカウント)]** ダイアログ ボックスを閉じます。

ストレージ アカウントの作成には数分かかる場合があります。 作成後、**[OK]** をクリックして、**[Add Storage Account (ストレージ アカウントの追加)]** ダイアログ ボックスを閉じると、使用可能なストレージ アカウントの一覧に新しいストレージ アカウントが追加されます。

## <a name="to-add-an-existing-storage-account-to-the-list"></a>既存のストレージ アカウントを一覧に追加するには
1. Azure ストレージ アカウントをまだ保有していない場合は、前の「**新しいストレージ アカウントを作成するには**」セクションの手順に従って、ストレージ アカウントを作成します  (または、[Microsoft Azure 管理ポータル][Azure Management Portal]で新しいストレージ アカウントを作成できます。)
2. **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスで、**[Add (追加)]** をクリックします。
3. **[Add Storage Account (ストレージ アカウントの追加)]** ダイアログ ボックスで、**[Name (名前)]** と **[Access Key (アクセス キー)]** の値を入力します。 アカウント名とアクセス キーは、既存の Azure ストレージ アカウントのものである必要があります。 [Microsoft Azure 管理ポータル][Azure Management Portal]の **[ストレージ]** セクションで、ストレージ アカウントの名前とキーを確認します。 **[ストレージ アカウントの追加]** ダイアログ ボックスは、次のようになります。
   
    ![][ic719497]
4. **[OK]** をクリックして、**[Add Storage Account (ストレージ アカウントの追加)]** ダイアログ ボックスを閉じます。

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>新しいアクセス キーを使用するためにストレージ アカウントを変更するには
1. **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスで、編集するストレージ アカウントをクリックし、**[Edit (編集)]** をクリックします。
2. **[Edit Storage Account Access Key (ストレージ アカウントのアクセス キーの編集)]** ダイアログ ボックスで、**[Access Key (アクセス キー)]** の値を変更します。
3. **[OK]** をクリックして、**[Edit Storage Account Access Key (ストレージ アカウントのアクセス キーの編集)]** ダイアログ ボックスを閉じます。

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Eclipse で保持されている一覧からストレージ アカウントを削除するには
1. **[Storage Accounts (ストレージ アカウント)]** ダイアログ ボックスで、編集するストレージ アカウントをクリックし、**[Remove (削除)]** をクリックします。
2. ストレージ アカウントの削除を求めるメッセージが表示されたら、 **[OK]** をクリックします。

> [!NOTE]
> **[ストレージ アカウント]** ダイアログ ボックスでのストレージ アカウントの削除は、Eclipse 内で表示できるストレージ アカウントの一覧からのみアカウントを削除します。 ストレージ アカウントは、Azure サブスクリプションからは削除されません。 また、ストレージ アカウントは、Eclipse でサブスクリプションの詳細を再読み込みした後に、再度表示される場合があります。
> 
> 

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure Toolkit for Eclipse のインストール][Installing the Azure Toolkit for Eclipse] 

[Azure 向け Hello World アプリケーションを Eclipse で作成する][Creating a Hello World Application for Azure in Eclipse]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java Developer Center]を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->



<!--HONumber=Jan17_HO1-->


