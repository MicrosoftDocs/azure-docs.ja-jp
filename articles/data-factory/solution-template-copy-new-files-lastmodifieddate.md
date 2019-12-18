---
title: LastModifiedDate に基づいて新規および変更済みのファイルをコピーする
description: Azure Data Factory で LastModifiedDate に基づいて新規および変更済みのファイルをコピーするソリューション テンプレートの使用方法について説明します。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942097"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Azure Data Factory で LastModifiedDate に基づいて新規および変更済みのファイルをコピーする

この記事では、LastModifiedDate 基づいて新規および変更済みのファイルのみをファイル ベースのストアからコピー先ストアにコピーするために使用できるソリューション テンプレートについて説明します。 

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートは、最初に **LastModifiedDate** 属性に基づいて新規および変更済みのファイルのみを選択してから、選択したファイルをソース データ ストアからコピー先データ ストアにコピーします。

このテンプレートには、以下の 1 つのアクティビティが含まれています。
- **コピー**。LastModifiedDate に基づいて新規および変更済みのファイルのみをファイル ストアからコピー先ストアにコピーします。

このテンプレートでは、次の 6 つのパラメーターが定義されます。
-  *FolderPath_Source* は、ソース ストアからファイルを読み込むために使用するフォルダー パスです。 既定値を目的のフォルダー パスに置き換える必要があります。
-  *Directory_Source* は、ソース ストアからファイルを読み取ることができるサブフォルダー パスです。 既定値を独自のサブフォルダー パスに置き換える必要があります。
-  *FolderPath_Destination* は、コピー先ストアにファイルをコピーするために使用するフォルダー パスです。 既定値を目的のフォルダー パスに置き換える必要があります。
-  *Directory_Destination* は、ファイルを宛先ストアにコピーできるサブフォルダー パスです。 既定値を独自のサブフォルダー パスに置き換える必要があります。
-  *LastModified_From* は、LastModifiedDate 属性がこの datetime 値と同じかそれ以降であるファイルを選択するために使用します。  前回コピーされなかった新しいファイルのみを選択するには、この datetime 値を前回パイプラインがトリガーされた時刻に設定します。 既定値の '2019-02-01T00:00:00Z' を UTC タイムゾーンの予想される LastModifiedDate に置き換えることができます。 
-  *LastModified_To* は、LastModifiedDate 属性がこの datetime 値より前であるファイルを選択するために使用します。 前回コピーされなかった新しいファイルのみを選択するには、この datetime 値を現在の時刻に設定します。  既定値の '2019-02-01T00:00:00Z' を UTC タイムゾーンの予想される LastModifiedDate に置き換えることができます。 

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. テンプレート **Copy new files only by LastModifiedDate (LastModifiedDate に基づいて新しいファイルのみをコピーする)** に移動します。 ソース ストレージ ストアへの **[新規]** 接続を作成します。 ソース ストレージ ストアは、コピーするファイルが存在する場所です。

    ![ソースへの新しい接続を作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. コピー先ストアへの **[新規]** 接続を作成します。 コピー先ストアは、ファイルのコピー先となる場所です。 

    ![コピー先への新しい接続を作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. **[このテンプレートを使用]** を選択します。

    ![このテンプレートを使用](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 次の例に示すように、使用可能なパイプラインがパネルに表示されます。

    ![パイプラインを表示する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. **[デバッグ]** を選択して **[パラメーター]** の値を書き込み、 **[完了]** を選択します。  以下の画像では、パラメーターを次のように設定しています。
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = subfolder
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = subfolder
   - **LastModified_From** =  2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    この例は、ある期間 (**2019-02-01T00:00:00Z** から **2019-03-01T00:00:00Z** まで) の間に最終変更されたファイルがソース パス **sourcefolder/subfolder** から宛先パス **destinationfolder/subfolder** にコピーされることを示しています。  これらは、独自のパラメーターに置き換えることができます。

    ![パイプラインを実行する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 結果を確認します。 構成された期間中に最終変更されたファイルのみがコピー先ストアにコピーされたことがわかります。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. タンブリング ウィンドウ トリガーを追加してこのパイプラインを自動化すると、パイプラインで常に LastModifiedDate に基づいて新規および変更済みのファイルのみを定期的にコピーできます。  **[トリガーの追加]** 、 **[新規/編集]** の順に選択します。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. **[Add Triggers]\(トリガーの追加\)** ウィンドウで **[+ 新規]** を選択します。

9. トリガーの種類として **[タンブリング ウィンドウ]** を選択し、繰り返しとして **[15 分ごと]** を設定します (任意の間隔時間に変更できます)。 [アクティブ化済み] ボックスで **[はい]** を選択してから、 **[OK]** を選択します。

    ![トリガーを作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. **[トリガー実行のパラメーター]** の値を次のように設定し、 **[完了]** を選択します。
    - **FolderPath_Source** = **sourcefolder**。  ソース データ ストアのフォルダーに置き換えることができます。
    - **Directory_Source** = **subfolder**。  ソース データ ストア内のサブフォルダーに置き換えることができます。
    - **FolderPath_Destination** = **destinationfolder**。  コピー先データ ストアのフォルダーに置き換えることができます。
    - **Directory_Destination** = **subfolder**。  宛先データ ストア内のサブフォルダーに置き換えることができます。
    - **LastModified_From** =   **\@trigger().outputs.windowStartTime**。  これは、パイプラインが最後にトリガーされた時間を特定するトリガーのシステム変数です。
    - **LastModified_To** =  **\@trigger().outputs.windowEndTime**。  これは、今回パイプラインがトリガーされる時間を特定するトリガーのシステム変数です。
    
    ![入力パラメーター](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. **[すべて公開]** を選択します。
    
    ![すべてを公開](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. ソース データ ストアのソース フォルダーに新しいファイルを作成します。  パイプラインが自動的にトリガーされるまで待機します。新しいファイルのみがコピー先ストアにコピーされます。

13. 左側のナビゲーション パネルの **[監視]** タブを選択し、トリガーの繰り返しが 15 分ごとに設定されている場合は約 15 分待ちます。 

14. 結果を確認します。 パイプラインが 15 分ごとに自動的にトリガーされ、パイプラインが実行されるたびにソース ストアの新規または変更済みのファイルのみがコピー先ストアにコピーされます。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)
