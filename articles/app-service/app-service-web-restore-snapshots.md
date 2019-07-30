---
title: バックアップからの復元 - Azure App Service
description: スナップショットからアプリを復元する方法について説明します。
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: ed659e95289665b6ce63ba6961e9f63650b4accf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617537"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Azure でスナップショットからアプリケーションを復元する
この記事では、[Azure App Service](../app-service/overview.md) でスナップショットからアプリを復元する方法について説明します。 アプリのスナップショットの 1 つに基づいて、アプリを以前の状態に復元することができます。 スナップショットのバックアップを有効にする必要はありません。データ復旧の目的で、すべてのアプリのスナップショットが自動的に保存されます。

スナップショットは増分シャドウ コピーであり、通常の[バックアップ](manage-backup.md)よりも優れている利点がいくつかあります。
- ファイル ロックによるファイル コピーエラーがありません。
- ストレージ サイズの制限がありません。
- 構成が必要ありません。

スナップショットからの復元は、**Premium** レベル以上で実行されているアプリで利用できます。 アプリのスケーリングについて詳しくは、「 [Azure App Service の Web アプリをスケーリングする](web-sites-scale.md)」をご覧ください。

## <a name="limitations"></a>制限事項

- 現在、この機能はプレビュー段階にあります。
- 同じアプリまたはそのアプリに属するスロットにのみ復元することができます。
- 復元の実行中、App Service はターゲット アプリまたはターゲット スロットを停止します。
- App Service は、プラットフォームのデータ復旧の目的で 3 か月分のスナップショットを保持しています。
- 過去 30 日間のスナップショットのみを復元できます。
- App Service 環境で実行される App Services は、スナップショットをサポートしていません。
 

## <a name="restore-an-app-from-a-snapshot"></a>アプリをスナップショットから復元する

1. [Azure Portal](https://portal.azure.com) にあるアプリの **[設定]** ページで、 **[バックアップ]** をクリックして **[バックアップ]** ページを表示します。 次に、 **[スナップショット (プレビュー)]** セクションにある **[復元]** をクリックします。
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. **[復元]** ページで、復元するスナップショットを選択します。
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. **[復元先]** でアプリの復元先を指定します。
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > **[上書き]** を選択した場合、アプリの現在のファイル システム内にある既存のデータはすべて消去され、上書きされます。 **[OK]** をクリックする前に、実行する操作内容を確認します。
   > 
   > 
      
   > [!Note]
   > 現在は技術的な制限があるため、同じスケール ユニット内のアプリにのみ復元することができます。 この制限は、今後のリリースで解消される予定です。
   > 
   > 
   
    **[既存のアプリ]** を選択してスロットに復元することができます。 このオプションを使用する前に、アプリにスロットを作成しておく必要があります。

4. サイト構成の復元を選択することができます。
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Click **OK**.
