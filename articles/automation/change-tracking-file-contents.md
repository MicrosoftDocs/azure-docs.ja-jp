---
title: Azure Automation でファイル コンテンツの変更を表示する
description: Change Tracking のファイル コンテンツの変更機能を使用して、変更されたファイルのコンテンツを表示できます。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ab88aa2dc604172f00d875353dabba61fd101af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850586"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Change Tracking で追跡されているファイルのコンテンツを表示する

ファイル コンテンツの追跡を使用すると、Change Tracking で追跡されているファイルの変更前と後のコンテンツを表示できます。 これを行うため、変更が発生するたびにファイル コンテンツがストレージ アカウントに保存されます。

## <a name="requirements"></a>必要条件

* ファイル コンテンツの格納には、Resource Manager デプロイ モデルを使用している Standard ストレージ アカウントが必要です。 Premium およびクラシック デプロイ モデルのストレージ アカウントは、使用しないでください。 ストレージ アカウントの詳細については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」を参照してください。

* 使用するストレージ アカウントには、Automation アカウントを 1 つだけ接続できます。

* [Change Tracking](automation-change-tracking.md) は、Automation アカウントで有効になっています。

## <a name="enable-file-content-tracking"></a>ファイル コンテンツの追跡の有効化

1. Azure portal で、Automation アカウントを開き、 **[変更の追跡]** を選択します。
2. 上部のメニューで、 **[設定の編集]** を選択します。
3. **[ファイル コンテンツ]** を選択し、 **[リンク]** をクリックします。 これにより **[Change Tracking の対象となるコンテンツの場所を追加]** ウィンドウが開きます。

   ![enable](./media/change-tracking-file-contents/enable.png)

4. サブスクリプションと、ファイル コンテンツを格納するために使用するストレージ アカウントを選択します。 既存の追跡対象ファイルすべてに対してファイル コンテンツの追跡を有効にする場合は、 **[すべての設定のファイル コンテンツをアップロードする]** で **[オン]** を選択します。 これは後からファイル パスごとに変更することができます。

   ![ストレージ アカウントの設定](./media/change-tracking-file-contents/storage-account.png)

5. 有効にすると、ストレージ アカウントと SAS URI が表示されます。 SAS URI は 365 日後に有効期限切れになり、 **[再生成]** ボタンをクリックして再作成することができます。

   ![アカウント キーの一覧表示](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>ファイルの追加

次の手順では、ファイルの変更の追跡を有効化する手順を説明します。

1. **Change Tracking** の **[設定の編集]** ページで、 **[Windows ファイル]** または **[Linux ファイル]** のいずれかのタブを選択し、 **[追加]** をクリックします。

1. ファイル パスの情報を入力し、 **[すべての設定のファイル コンテンツをアップロードする]** の下で **[True]** を選択します。 この設定は、そのファイル パスに対してのみファイル コンテンツの追跡を有効にします。

   ![Linux ファイルの追加](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>追跡対象ファイルのコンテンツの表示

1. ファイルまたはパスのファイルの変更が検出されると、ポータルに表示されます。 変更の一覧から、ファイルの変更を選択します。 **[変更の詳細]** ウィンドウが表示されます。

   ![変更の一覧表示](./media/change-tracking-file-contents/change-list.png)

1. **[変更の詳細]** ページで、ファイル情報の前後の標準を確認し、左上の **[ファイル コンテンツの変更の表示]** をクリックして、ファイルのコンテンツを確認します。

   ![変更の詳細](./media/change-tracking-file-contents/change-details.png)

1. 新しいページにファイル コンテンツが左右に並んで表示されます。 **[インライン]** を選択して、変更のインライン ビューを表示することもできます。

   ![ファイルの変更の表示](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>次の手順

ソリューションの使用方法の詳細については、Change Tracking のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [環境の変更に関する問題を解決する](automation-tutorial-troubleshoot-changes.md)

* [Azure Monitor ログのログ検索](../log-analytics/log-analytics-log-searches.md)を使用して、詳細な変更追跡データを確認します。

