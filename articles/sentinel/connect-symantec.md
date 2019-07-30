---
title: Azure Sentinel プレビューに Symantec ICDx データを接続する | Microsoft Docs
description: Azure Sentinel に Symantec ICDx データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673752"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDx アプライアンスを接続する 

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Symantec ICDx コネクタを使用すると、Azure Sentinel にすべての Symantec セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 Symantec ICDx と Azure Sentinel との統合には、REST API を使用します。


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx の構成および接続 

Symantec ICDx では、ログを統合し、Azure Sentinel に直接エクスポートすることができます。

1. ICDx 管理コンソールを開き、Microsoft Azure Sentinel (Log Analytics) フォワーダーを追加します。
2. あるいは、ICDx ナビゲーション バーで **[構成]** をクリックします。 
3. **[構成]** 画面の上部で **[フォワーダー]** をクリックします。
4. **[フォワーダー]** の下で、Microsoft Azure Sentinel (Log Analytics) の隣にある **[追加]** をクリックします。 
4. **[Microsoft Azure Sentinel (Log Analytics)]** ウィンドウで、 **[詳細設定の表示]** をクリックします。 
5. [Microsoft Azure Sentinel (Log Analytics)] ウィンドウを拡張したら、さらに次を行います。
    -   **[名前]** :フォワーダーに 30 文字以下の名前を入力します。 一意でわかりやすい名前を選択してください。 この名前は、 **[構成]** 画面のフォワーダーの一覧と **[ダッシュボード]** 画面のダッシュボードに表示されます。 例: Microsoft Azure Log Analytics East。 このフィールドは必須です。
    -   **説明**:フォワーダーの説明を入力します。 この説明も、 **[構成]** 画面のフォワーダーの一覧に表示されます。 転送されるイベントの種類やデータを検査する必要があるグループなどの詳細を含めます。
    -   **スタートアップの種類**:フォワーダー構成のスタートアップ方法を選択します。 オプションは手動と自動です。<br>既定値は自動です。 
6. **[イベント]** の下で次を行います。 
    - **ソース**:イベントの転送元として 1 つまたは複数のアーカイブを選択します。 アクティブなコレクター アーカイブ (共通アーカイブを含む)、孤立コレクター アーカイブ (つまり、削除したコレクターのアーカイブ)、ICDx レシーバー アーカイブ、システム アーカイブを選択できます。 <br>既定は共通アーカイブです。
      > [!NOTE]
      > ICDx レシーバー アーカイブは名前別に一覧表示されます。 
 
    - **フィルター**:転送するイベントのサブセットを指定するフィルターを追加します。 次のいずれかを実行します。
        - フィルター条件を選択するには、型、属性、演算子、値をクリックします。 
        - [フィルター] フィールドで、フィルター条件を確認します。 条件はフィールドで直接編集するか、必要に応じて削除できます。
        - フィルター条件を追加するには、[AND] または [OR] をクリックします。
        - [保存済みのクエリ] をクリックし、保存済みのクエリを適用することもできます。
    - **含まれる属性**:転送されるデータに含める属性のコンマ区切りリストを入力します。 含まれる属性は、除外される属性によりも優先されます。
    - **除外される属性**:転送されるデータから除外される属性のコンマ区切りリストを入力します。
    - **バッチ サイズ**:バッチごとに送信するイベントの数を選択します。 10、50、100、500、1000 から選択できます。<br>既定値は 100 です。 
    - **レート制限**:イベントが転送されるレートを選択します。これは秒あたりのイベント数で表されます。 無制限、500、1000、5000、10000 から選択できます。 <br> 既定値は 5000 です。 
7. **[Azure 宛先]** の下で次を行います。 
    - **ワークスペース ID**:下からワークスペース ID を貼り付けます。 このフィールドは必須です。
    - **主キー**:下から主キーを貼り付けます。 このフィールドは必須です。
    - **カスタム ログの名前**:イベントの転送先とする Microsoft Azure portal Log Analytics ワークスペースにカスタム ログの名前を入力します。 既定値は SymantecICDx です。 このフィールドは必須です。
8. *[保存]* をクリックし、フォワーダー構成を完了します。 
9. フォワーダーを開始するには、 **[オプション]** の下で、 **[詳細]** をクリックし、 **[開始]** をクリックします。
10. Log Analytics で Symantec ICDx イベントに関連するスキーマを使用するには、**SymantecICDx_CL** を検索します。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 



## <a name="next-steps"></a>次の手順
このドキュメントでは、Symantec ICDx を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

