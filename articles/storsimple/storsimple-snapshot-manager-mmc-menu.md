---
title: StorSimple Snapshot Manager の MMC メニュー操作 | Microsoft Docs
description: StorSimple Snapshot Manager で、Microsoft 管理コンソール (MMC) の標準のメニュー操作を使用する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90056005"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager での MMC メニュー操作の使用

## <a name="overview"></a>概要
StorSimple Snapshot Manager では、すべての操作メニューと **[操作]** ウィンドウのすべてのバリエーションに次の操作が表示されます。

* 表示
* ここから新しいウィンドウ 
* 更新 
* リストのエクスポート 
* Help 

これらの操作は Microsoft 管理コンソール (MMC) の機能であり、StorSimple Snapshot Manager に固有のものではありません。 このチュートリアルでは、これらの操作について説明し、StorSimple Snapshot Manager での各操作の使用方法を示します。

## <a name="view"></a>表示
[**表示**] オプションを使用して、[**結果**] ウィンドウの表示と、コンソール ウィンドウの表示を変更できます。 

#### <a name="to-change-the-results-pane-view"></a>結果ウィンドウの表示を変更するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、任意のノードを右クリックするか、またはノードを展開して **[結果]** ウィンドウの項目を右クリックし、**[表示]** オプションをクリックします。 
3. **[結果]** ウィンドウに表示される列を追加または削除するには、**[列の追加と削除]** をクリックします。 **[列の追加と削除]** ダイアログ ボックスが表示されます。
   
    ![結果ウィンドウの列の追加または削除](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. 次のようにフォームの操作を実行します。
   
   * **[利用可能な列]** の一覧で項目を選択し、**[追加]** をクリックして **[表示される列]** の一覧に追加します。 
   * **[表示する列]** の一覧から項目を選択し、**[削除]** をクリックして一覧から削除します。 
   * **[表示される列]** の一覧で項目を選択し、**[上へ移動]** または **[下へ移動]** をクリックして、一覧内で項目を上下に移動します。 
   * **[既定値に戻す]** をクリックして、**[結果]** ウィンドウを既定の構成に戻します。 
5. 項目の選択が完了したら、**[OK]** をクリックします。 

#### <a name="to-change-the-console-window-view"></a>コンソール ウィンドウの表示を変更するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、任意のノードを右クリックし、**[表示]** をクリックして、**[カスタマイズ]** をクリックします。 **[カスタマイズ]** ダイアログ ボックスが表示されます。
   
    ![コンソール ウィンドウのカスタマイズ](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. チェック ボックスをオンまたはオフにして、コンソール ウィンドウの項目の表示/非表示を切り替えます。 項目の選択が完了したら、**[OK]** をクリックします。

## <a name="new-window-from-here"></a>ここから新しいウィンドウ
[**ここから新しいウィンドウ**] オプションを使用すると、新しいコンソール ウィンドウを開くことができます。

#### <a name="to-open-a-new-console-window"></a>新しいコンソール ウィンドウを開くには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、任意のノードを右クリックし、**[ここから新しいウィンドウ]** をクリックします。 
   
    新しいウィンドウが開き、選択したスコープだけが表示されます。 たとえば、**[バックアップ ポリシー]** ノードを右クリックして [ここから新しいウィンドウ] をクリックすると、新しいウィンドウには、**[スコープ]** ウィンドウに **[バックアップ ポリシー]** ノードのみが表示され、定義済みのバックアップ ポリシーの一覧が **[結果]** ウィンドウに表示されます。 次の例を参照してください。
   
    ![ここから新しいウィンドウ](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>更新
[**更新**] アクションを使用して、コンソール ウィンドウを更新できます。

#### <a name="to-update-the-console-window"></a>コンソール ウィンドウを更新するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、任意のノードを右クリックするか、またはノードを展開して **[結果]** ウィンドウの項目を右クリックし、**[最新の情報に更新]** をクリックします。 

## <a name="export-list"></a>リストのエクスポート
[**一覧のエクスポート**] アクションを使用して、一覧をコンマ区切り値 (CSV) ファイルに保存できます。 たとえば、バックアップ ポリシーのリストやバックアップ カタログをエクスポートできます。 その後、CSV ファイルをスプレッドシート アプリケーションにインポートして分析することができます。

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>リストをコンマ区切り値 (CSV) ファイルに保存するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。 
2. **[スコープ]** ウィンドウで、任意のノードを右クリックするか、またはノードを展開して **[結果]** ウィンドウの項目を右クリックし、**[一覧のエクスポート]** をクリックします。 
3. **[一覧のエクスポート]** ダイアログ ボックスが表示されます。 次のようにフォームの操作を実行します。 
   
   1. **[ファイル名]** ボックスで、CSV ファイルの名前を入力するか、または矢印をクリックしてドロップダウン リストから選択します。
   2. **[ファイルの種類]** ボックスで、矢印をクリックしてドロップダウン リストからファイルの種類を選択します。
   3. 選択した項目のみを保存するには、行を選択して、**[選択した行のみ保存]** チェック ボックスをオンにします。 エクスポートされたすべてのリストを保存するには、 **[選択した行のみ保存]** チェック ボックスをオフにします。
   4. **[保存]** をクリックします。
      
      ![リストをコンマ区切り値ファイルとしてエクスポート](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
**[ヘルプ]** メニューを使用して、StorSimple Snapshot Manager と MMC で利用可能なオンライン ヘルプを表示できます。

#### <a name="to-view-available-online-help"></a>利用可能なオンライン ヘルプを表示するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、任意のノードを右クリックするか、またはノードを展開して **[結果]** ウィンドウの項目を右クリックし、**[ヘルプ]** をクリックします。 

## <a name="next-steps"></a>次のステップ
* [StorSimple Snapshot Manager のユーザー インターフェイスの詳細](storsimple-use-snapshot-manager.md)
* [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理の詳細](storsimple-snapshot-manager-admin.md)

