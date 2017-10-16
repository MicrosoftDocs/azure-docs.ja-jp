---
title: "Azure Stack で更新プログラムを適用する | Microsoft Docs"
description: "Azure Stack 統合システム用の Microsoft 更新プログラム パッケージをインポートしてインストールする方法を説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 8fbed3c15b385e385e64093331df37363a558185
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="apply-updates-in-azure-stack"></a>Azure Stack で更新を適用する

*適用対象: Azure Stack 統合システム*

Azure Stack オペレーターは、管理者ポータルの [更新] タイルを使用して、Azure Stack 用の更新プログラム パッケージを適用できます。 Microsoft 更新プログラム パッケージをダウンロードし、パッケージ ファイルを Azure Stack にインポートした後、更新プログラム パッケージをインストールする必要があります。 

## <a name="download-the-update-package"></a>更新プログラム パッケージをダウンロードする

Azure Stack 用の Microsoft 更新プログラム パッケージを利用できるときに、Azure Stack からアクセスできる場所にパッケージをダウンロードし、パッケージの内容を確認します。 通常、更新プログラム パッケージは、次のファイルで構成されます。

- 自己解凍形式の *PackageName*.exe ファイル。 このファイルには、更新用のペイロード (Windows Server の最新の累積更新プログラムなど) が含まれます。   
- 対応する *PackageName*.bin ファイル。 これらのファイルは、*PackageName*.exe ファイルに関連付けられているペイロードの圧縮を提供します。 
- Metadata.xml ファイル。 このファイルには、更新プログラムに関する重要な情報 (発行元、名前、前提条件、サイズ、サポート パス URL など) が含まれます。

## <a name="import-and-install-updates"></a>更新プログラムをインポートしてインストールする

次の手順は、管理者ポータルで更新プログラム パッケージをインポートしてインストールする方法を示しています。

1. 管理ポータルで、**[その他のサービス]** を選択します。 次に、**[データ + ストレージ]** カテゴリで、**[ストレージ アカウント]** を選択します  (または、フィルター ボックスで「**ストレージ アカウント**」の入力を開始して選択します)。

    ![ポータルでストレージ アカウントを探す場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. フィルター ボックスに「**更新**」と入力し、**updateadminaccount** ストレージ アカウントを選択します。

    ![Updateadminaccount を検索する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. ストレージ アカウントの詳細で、**[サービス]** の **[BLOB]** を選択します。
 
    ![ストレージ アカウントの BLOB を選択する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. **[Blob service]** で、**[+ コンテナー]** を選択してコンテナーを作成します。 名前 (例: *Update-1709*) を入力し、**[OK]** を選択します。
 
     ![ストレージ アカウントにコンテナーを追加する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. コンテナーが作成されたら、コンテナー名をクリックした後、**[アップロード]** をクリックしてパッケージ ファイルをコンテナーにアップロードします。
 
    ![パッケージ ファイルをアップロードする方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. **[BLOB のアップロード]** で、フォルダー アイコンをクリックし、更新プログラム パッケージの .exe ファイルを参照し、ファイル エクスプローラー ウィンドウで **[開く]** をクリックします。
  
7. **[BLOB のアップロード]** で、**[アップロード]** をクリックします。 
 
    ![各パッケージ ファイルをアップロードする場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. *PackageName*.bin ファイルと Metadata.xml ファイルに対して、手順 6 と 7 を繰り返します。 
9. 終了したら、通知を確認できます (ポータルの右上隅にあるベル アイコン)。 アップロードの完了が通知に示されている必要があります。 
10. ダッシュボードの [更新] タイルに戻ります。 更新プログラムが使用可能であることがタイルに示されている必要があります。 新しく追加された更新プログラム パッケージを確認するには、タイルをクリックします。
11. 更新プログラムをインストールするには、**[準備完了]** とマークされているパッケージを選択し、パッケージを右クリックして **[今すぐ更新]** を選択するか、上部にある **[今すぐ更新]** アクションをクリックします。
12. 更新プログラム パッケージのインストールをクリックしたら、**[Update run details]\(更新実行の詳細\)** 領域でその状態を確認できます。 ここから **[Download full logs]\(満杯になったログのダウンロード\)**をクリックして、ログ ファイルをダウンロードすることもできます。
13. 更新プログラムが完了すると、更新された Azure Stack のバージョンが [更新] タイルに表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)