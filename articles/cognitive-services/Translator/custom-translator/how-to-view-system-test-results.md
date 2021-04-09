---
title: システムのテスト結果とデプロイを確認する - Custom Translator
titleSuffix: Azure Cognitive Services
description: トレーニングが成功したら、システムのテストを確認し、トレーニング結果を分析します。 トレーニング結果に満足したら、トレーニング済みモデルのデプロイを要求します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: cae2c95e56312c58d396d1e578f4677ce2b14aa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895936"
---
# <a name="view-system-test-results"></a>システムのテスト結果の表示

トレーニングが成功したら、システムのテストを確認し、トレーニング結果を分析します。 トレーニング結果に満足したら、トレーニング済みモデルのデプロイを要求します。

## <a name="system-test-results-page"></a>システムのテスト結果ページ

プロジェクトを選択し、そのプロジェクトのモデル タブを選択し、使用するモデルを見つけて、最後にテスト タブを選択します。

テスト タブには次の内容が表示されます。

1.  **System Test Results (システムのテスト結果):** トレーニングでのテスト プロセスの結果。 テスト プロセスで BLEU スコアが生成されます。

    **Sentence Count (文の数):** テスト セットで使用された並列文の数。

     **BLEU Score (BLEU スコア):** トレーニング完了後にモデルに対して生成された BLEU スコア。

    **状態:** テスト プロセスが完了したか進行中かが示されます。

    ![システムのテスト結果](media/how-to/how-to-system-test-results.png)

2.  [System Test Results]\(システムのテスト結果\) をクリックします。テスト結果の詳細ページが表示されます。 このページには、テスト データ セットに含まれていた文の機械翻訳が表示されます。

3.  テスト結果の詳細ページの表には、2 つの列があります。ペアの各言語の列です。 ソース言語の列には、翻訳対象の文が表示されます。 ターゲット言語の列には、各行に 2 つの文が含まれています。

    **Ref:** この文は、テスト データセットで指定された原文の参考訳です。

    **MT:** この文は、トレーニングが実行された後に構築されたモデルによって行われた原文の自動翻訳です。

    ![システムのテスト結果の比較](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>テストをダウンロードする

[Download Translations]\(翻訳のダウンロード\) リンクをクリックして zip ファイルをダウンロードします。 この zip には、テスト データ セット内の原文の機械翻訳が含まれています。

![テストをダウンロードする](media/how-to/how-to-system-test-download.png)

このダウンロードされた zip アーカイブには 3 つのファイルが含まれています。

1.  **custom.mt.txt:** このファイルには、ユーザーのデータを使用してトレーニングしたモデルによって実行された、ソース言語の文からターゲット言語への機械翻訳が含まれています。

2.  **ref.txt:** このファイルには、ソース言語の文からターゲット言語へのユーザーが指定した翻訳が含まれています。

3.  **source.txt:** このファイルには、ソース言語の文が含まれています。

    ![システムのテスト結果をダウンロードする](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>モデルをデプロイする

デプロイを要求するには:

1.  プロジェクトを選択し、[モデル] タブに移動します。

2. 正常にトレーニングが完了したモデルで、まだデプロイされていない場合は、[デプロイ] ボタンが表示されます。

    ![モデルをデプロイするための [デプロイ] ボタンが強調表示されているスクリーンショット。](media/how-to/how-to-deploy-model.png)

3.  [デプロイ] をクリックします。
4.  モデルをデプロイするリージョンの **[Deployed]\(デプロイ対象\)** を選択し、[保存] をクリックします。 複数のリージョンの **[Deployed]\(デプロイ対象\)** を選択することができます。

    ![モデルをデプロイまたはデプロイ解除する場所を示すスクリーンショット。](media/how-to/how-to-deploy-model-regions.png)

5.  モデルの状態は [状態] 列で確認できます。

>[!Note]
>カスタム翻訳ツールは、任意の時点で、ワークスペース内の 10 個のデプロイ済みモデルをサポートします。

## <a name="update-deployment-settings"></a>デプロイの設定を更新する

デプロイの設定を更新するには:

1.  プロジェクトを選択し、 **[モデル]** タブに移動します。

2. 正常にデプロイされたモデルには、 **[更新]** ボタンが表示されます。

    ![デプロイ設定を更新するための [更新] ボタンが強調表示されているスクリーンショット。](media/how-to/how-to-update-undeploy-model.png)

3.  **[Update]\(更新\)** を選択します。
4.  モデルをデプロイまたはデプロイ解除するリージョンの **[Deployed]\(デプロイ対象\)** または **[Undeployed]\(デプロイ解除対象\)** を選択して、 **[Save]\(保存\)** をクリックします。

    ![モデルのデプロイ](media/how-to/how-to-undeploy-model.png)

>[!Note]
>すべてのリージョンについて **[Undeployed]\(デプロイ解除対象\)** を選択した場合、モデルはすべてのリージョンからデプロイ解除されて、デプロイ解除済み状態にされます。 それは使用できなくなります。

## <a name="next-steps"></a>次のステップ

- [Microsoft Translator Text API V3](../reference/v3-0-translate.md?tabs=curl) を介してデプロイされた翻訳モデルを使い始めます。
- [設定を管理](how-to-manage-settings.md)してワークスペースを共有し、サブスクリプション キーを管理する方法について説明します。
- [Microsoft Translator Hub](https://hub.microsofttranslator.com) から[ワークスペースとプロジェクトを移行する方法](how-to-migrate.md)について説明します。