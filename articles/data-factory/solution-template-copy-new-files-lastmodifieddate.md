---
title: Azure Data Factory で LastModifiedDate に基づいて新規および変更済みのファイルをコピーする | Microsoft Docs
description: Azure Data Factory で LastModifiedDate に基づいて新規および変更済みのファイルをコピーするソリューション テンプレートの使用方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111941"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Azure Data Factory で LastModifiedDate に基づいて新規および変更済みのファイルをコピーする

この記事では、LastModifiedDate 基づいて新規および変更済みのファイルのみをファイル ベースのストアからコピー先ストアにコピーするために使用できるソリューション テンプレートについて説明します。 

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートは、最初に **LastModifiedDate** 属性に基づいて新規および変更済みのファイルのみを選択してから、選択したファイルをソース データ ストアからコピー先データ ストアにコピーします。

このテンプレートには、以下の 1 つのアクティビティが含まれています。
- **コピー**。LastModifiedDate に基づいて新規および変更済みのファイルのみをファイル ストアからコピー先ストアにコピーします。

このテンプレートには、4 つのパラメーターが定義されています。
-  *FolderPath_Source* は、ソース ストアからファイルを読み込むために使用するフォルダー パスです。 既定値を目的のフォルダー パスに置き換える必要があります。
-  *FolderPath_Destination* は、コピー先ストアにファイルをコピーするために使用するフォルダー パスです。 既定値を目的のフォルダー パスに置き換える必要があります。
-  *LastModified_From* は、LastModifiedDate 属性がこの datetime 値と同じかそれ以降であるファイルを選択するために使用します。  前回コピーされなかった新しいファイルのみを選択するには、この datetime 値を前回パイプラインがトリガーされた時刻に設定します。 既定値の '2019-02-01T00:00:00Z' を UTC タイムゾーンの予想される LastModifiedDate に置き換えることができます。 
-  *LastModified_To* は、LastModifiedDate 属性がこの datetime 値より前であるファイルを選択するために使用します。 前回コピーされなかった新しいファイルのみを選択するには、この datetime 値を現在の時刻に設定します。  既定値の '2019-02-01T00:00:00Z' を UTC タイムゾーンの予想される LastModifiedDate に置き換えることができます。 

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. テンプレート **Copy new files only by LastModifiedDate (LastModifiedDate に基づいて新しいファイルのみをコピーする)** に移動します。 ソース ストレージ ストアへの **[新規]** 接続を作成します。 ソース ストレージ ストアは、コピーするファイルが存在する場所です。

    ![ソースへの新しい接続を作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 最初に、ストレージの **[タイプ]** を選択します。 次に、ストレージの **[アカウント名]** と **[アカウント キー]** を入力します。 最後に、**[完了]** を選択します。

    ![接続文字列を入力する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. コピー先ストアへの **[新規]** 接続を作成します。 コピー先ストアは、ファイルのコピー先となる場所です。 手順 2 と同様に、コピー先データ ストアの接続情報を入力する必要があります。

    ![コピー先への新しい接続を作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. **[このテンプレートを使用]** を選択します。

    ![このテンプレートを使用](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. 次の例に示すように、使用可能なパイプラインがパネルに表示されます。

    ![パイプラインを表示する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. **[デバッグ]** を選択して **[パラメーター]** の値を書き込み、**[完了]** を選択します。  以下の画像では、パラメーターを次のように設定しています。
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     この例は、*2019-02-01T00:00:00Z* から *2019-03-01T00:00:00Z* までの期間中に最終変更されたファイルが */source/* フォルダーから */destination/* フォルダーにコピーされることを示しています。  これらは、独自のパラメーターに置き換えることができます。
    
     ![パイプラインを実行する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. 結果を確認します。 構成された期間中に最終変更されたファイルのみがコピー先ストアにコピーされたことがわかります。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. タンブリング ウィンドウ トリガーを追加してこのパイプラインを自動化すると、パイプラインで常に LastModifiedDate に基づいて新規および変更済みのファイルのみを定期的にコピーできます。  **[トリガーの追加]**、**[新規/編集]** の順に選択します。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. **[Add Triggers]\(トリガーの追加\)** ウィンドウで **[+ 新規]** を選択します。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. トリガーの種類として **[タンブリング ウィンドウ]** を選択し、繰り返しを **[15 分ごと]** に設定して (任意の時間間隔に変更できます)、**[次へ]** を選択します。

    ![トリガーを作成する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. **[トリガー実行のパラメーター]** の値を次のように書き込み、**[完了]** を選択します。
    - **FolderPath_Source** = **/source/**。  ソース データ ストアのフォルダーに置き換えることができます。
    - **FolderPath_Destination** = **/destination/**。  コピー先データ ストアのフォルダーに置き換えることができます。
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**。  これは、パイプラインが最後にトリガーされた時間を特定するトリガーのシステム変数です。
    - **LastModified_To** = **@trigger().outputs.windowEndTime**。  これは、今回パイプラインがトリガーされる時間を特定するトリガーのシステム変数です。
    
    ![入力パラメーター](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. **[すべて公開]** を選択します。
    
    ![すべてを公開](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. ソース データ ストアのソース フォルダーに新しいファイルを作成します。  パイプラインが自動的にトリガーされるまで待機します。新しいファイルのみがコピー先ストアにコピーされます。

14. 左側のナビゲーション パネルで **[監視]** タブを選択し、トリガーの繰り返しが 15 分ごとに設定されている場合は、15 分ほど待機します。 

    ![監視を選択する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. 結果を確認します。 パイプラインが 15 分ごとに自動的にトリガーされ、パイプラインが実行されるたびにソース ストアの新規または変更済みのファイルのみがコピー先ストアにコピーされます。

    ![結果を確認する](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)