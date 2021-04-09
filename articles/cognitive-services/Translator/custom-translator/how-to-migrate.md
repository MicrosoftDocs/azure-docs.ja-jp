---
title: Microsoft Translator Hub ワークスペースとプロジェクトの移行 - Custom Translator
titleSuffix: Azure Cognitive Services
description: この記事では、Hub ワークスペースとプロジェクトを Azure Cognitive Services Custom Translator に移行する方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895713"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Microsoft Translator Hub ワークスペースとプロジェクトを移行する

お客様の [Microsoft Translator Hub](https://hub.microsofttranslator.com/) ワークスペースおよびプロジェクトは、カスタム トランスレーターに簡単に移行することができます。 移行は Microsoft Hub から開始します。ワークスペースまたはプロジェクトを選択し、カスタム トランスレーターのワークスペースを選択してから、お客様が転送したいトレーニングを選択します。 移行の開始後、選択されたトレーニングの設定が、関連するすべてのドキュメントと共に転送されます。 デプロイされたモデルはトレーニングされ、完了すると自動デプロイされます。

移行中は以下のアクションが実行されます。
* すべてのドキュメントとプロジェクトの定義は、名前に "hub_" というプレフィックスが追加された状態で転送されます。 自動生成されたテストとチューニング データには、hub_systemtune_\<modelid> または hub_systemtest_\<modelid> という名前が付きます。
* 移行が実施されたときにデプロイ済み状態だったトレーニングはすべて、Hub トレーニングのドキュメントを使用して自動的にトレーニングされます。 このトレーニングによってお客様のサブスクリプションが課金されることはありません。 移行に自動デプロイが選択された場合、完了時にトレーニング済みのモデルがデプロイされます。 通常のホスティング料金は適用されます。
* 移行されたトレーニングのうち、デプロイ済み状態でなかったものはすべて、移行済みドラフト状態になります。 この状態では、移行済みの定義を使用してモデルをトレーニングするかどうかを選択できますが、通常のトレーニング料金が適用されます。
* Hub トレーニングから移行された BLEU スコアは、モデルの TrainingDetails ページ ([Bleu score in MT Hub]\(MT Hub における BLEU スコア\) という見出し) でいつでも確認できます。

> [!Note] 
> Custom Translator でトレーニングを正常に実行するには、一意の文が最低 10,000 個抽出されている必要があります。 [推奨される最小要件](./sentence-alignment.md#suggested-minimum-number-of-sentences)に満たない場合、Custom Translator ではトレーニングを実行できません。

## <a name="find-custom-translator-workspace-id"></a>カスタム トランスレーターのワークスペース ID を見つける

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) ワークスペースを移行するには、カスタム トランスレーターにおける移行先のワークスペース ID が必要です。 Custom Translator の移行先ワークスペースは、すべての Hub ワークスペースとプロジェクトを移行される場所です。

カスタム トランスレーターの [設定] ページで、お客様の移行先のワークスペース ID が見つかります。

1. Custom Translator ポータルの [設定] ページに移動します。

2. [基本情報] セクションにワークスペース ID があります。

    ![移行先のワークスペース ID を見つける方法](media/how-to/how-to-find-destination-ws-id.png)

3. 移行プロセス中に参照できるように、お客様の移行先のワークスペース ID をメモしておきます。

## <a name="migrate-a-project"></a>プロジェクトを移行する

プロジェクトを選択して移行する場合は、Microsoft Translator Hub にその機能があります。

1 つのプロジェクトを移行するには:

1. Microsoft Translator Hub にサインインします。

2. [プロジェクト] ページに移動します。

3. 適切なプロジェクトの [移行] リンクをクリックします。

    ![選択したプロジェクトの [移行] ボタンが強調表示されているスクリーンショット。](media/how-to/how-to-migrate-from-hub.png)

4. [移行] リンクをクリックするとフォームが表示され、以下の内容を入力できます。
   * お客様がカスタム トランスレーターに転送したいワークスペースを指定します。
   * 成功したすべてのトレーニングを転送するか、デプロイされたトレーニングのみを移行するかを指定します。 既定では、成功したすべてのトレーニングが転送されます。
   * トレーニングの完了時に、お客様のトレーニングを自動デプロイするかどうかを指定します。 既定では、お客様のトレーニングは完了後に自動デプロイされません。

5. [要求の送信] をクリックします。

## <a name="migrate-a-workspace"></a>ワークスペースを移行する

単一のプロジェクトを移行することに加え、成功したトレーニングを含むすべてのプロジェクトをワークスペースで移行することもできます。 この場合、ワークスペース内の各プロジェクトが、[移行] リンクをクリックしたときのように評価されます。 この機能は、カスタム トランスレーターにすべて同じ設定で移行したいプロジェクトを多数抱えるユーザーに適しています。 ワークスペースの移行は、Translator Hub の [設定] ページから開始できます。

ワークスペースを移行するには:

1. Microsoft Translator Hub にサインインします。

2. [設定] ページに移動します。

3. [設定] ページで [Migrate Workspace data to Custom Translator]\(ワークスペース データを Custom Translator に移行する\) をクリックします。

    ![[Migrate Workspace data to Custom Translator]\(ワークスペース データを Custom Translator に移行する\) オプションが強調表示されているスクリーンショット。](media/how-to/how-to-migrate-workspace-from-hub.png)

4. 次のページで、以下の 2 つのオプションのいずれかを選択します。

    a. Deployed Trainings only (デプロイされたトレーニングのみ): このオプションを選択すると、お客様のデプロイされたシステムと関連するドキュメントのみが移行されます。

    b. All Successful Trainings (成功したすべてのトレーニング): このオプションを選択すると、成功したすべてのトレーニングと関連するドキュメントが移行されます。

    c. Custom Translator の移行先のワークスペース ID を入力します。

    ![Hub から移行する方法](media/how-to/how-to-migrate-from-hub-screen.png)

5. [要求の送信] をクリックします。

## <a name="migration-history"></a>移行履歴

Hub からワークスペースまたはプロジェクトの移行を要求すると、カスタム翻訳ツールの [設定] ページで移行履歴を確認できます。

移行履歴を表示するには、次の手順を実行します。

1. Custom Translator ポータルの [設定] ページに移動します。

2. [設定] ページの [移行履歴] セクションで、[移行履歴] をクリックします。

    ![移行履歴](media/how-to/how-to-migration-history.png)

[移行履歴] ページには、要求した各移行の次の情報が概要として表示されます。

1. Migrated By (移行したユーザー): この移行要求を送信したユーザーの名前とメール アドレス

2. Migrated On (移行日): 移行の日付とタイム スタンプ

3. プロジェクト:移行が要求されたプロジェクトの数と、正常に移行されたプロジェクトの数。

4. トレーニング: 移行が要求されたトレーニングの数と、正常に移行されたトレーニングの数。

5. ドキュメント: 移行が要求されたドキュメントの数と、正常に移行されたドキュメントの数。

    ![移行履歴の詳細](media/how-to/how-to-migration-history-details.png)

プロジェクト、トレーニング、ドキュメントに関するより詳細な移行レポートが必要な場合は、詳細を CSV 形式でエクスポートするオプションがあります。

## <a name="implementation-notes"></a>実装に関するメモ
* システムの言語ペアにまだカスタム翻訳ツールが対応していない場合、そのシステムの利用は、カスタム翻訳ツールを通じてデータにアクセスするかデプロイを解除する目的に限定されます。 それらのプロジェクトは、[プロジェクト] ページに "利用不可" としてマークされます。 新しい言語ペアにカスタム翻訳ツールが対応した時点で、プロジェクトがアクティブになり、トレーニングとデプロイを行えるようになります。 
* Hub からカスタム トランスレーターにプロジェクトを移行しても、お客様の Hub のトレーニングまたはプロジェクトには一切影響がありません。 移行中、プロジェクトまたはドキュメントが Hub から削除されたり、モデルのデプロイが解除されたりすることはありません。
* 移行は、プロジェクトごとに 1 回だけ許可されます。 プロジェクトに対して移行を繰り返す必要がある場合は、Microsoft にお問い合わせください。
* Custom Translator では、英語との組み合わせに対応した複数の NMT 言語ペアがサポートされています。 [サポートされている言語の完全な一覧を確認してください](../language-support.md#customization)。 Hub にはベースライン モデルが不要であるため、数千の言語がサポートされます。 サポートされていない言語ペアを移行できますが、Microsoft が実施するのはドキュメントとプロジェクトの定義の移行のみです。 Microsoft で新しいモデルをトレーニングすることはできなくなります。 さらに、それらのドキュメントとプロジェクトは、現時点では使用できないことを示すために、非アクティブとして表示されます。 それらのプロジェクトまたはドキュメントは、今後サポートが追加されるとアクティブになってトレーニングできる状態になります。
* カスタム トランスレーターでは現在、モノリンガルのトレーニング データはサポートされていません。 サポートされていない言語ペアと同様、モノリンガルのドキュメントを移行することはできますが、サポートされるようになるまで、それらは非アクティブとして表示されます。
* カスタム トランスレーターでは、トレーニングに 10,000 個の並列文が必要です。 Microsoft Hub では、それよりも少ないデータでのトレーニングが可能です。 この要件を満たさないトレーニングを移行した場合、トレーニングされません。

## <a name="custom-translator-versus-hub"></a>Custom Translator と Hub

これは、Microsoft Translator Hub と Custom Translator の機能を比較する表です。

| 特徴量 | ハブ | Custom Translator |
| ------- | :-: | :---------------: |
| カスタマイズ機能の状態    | 一般公開    | 一般公開 |
| Text API バージョン    | V2     | V3  |
| SMT のカスタマイズ    | はい    | いいえ |
| NMT のカスタマイズ    | いいえ    | はい |
| 新しい統合 Speech Services のカスタマイズ    | いいえ    | はい |
| トレースなし | はい | はい |

## <a name="new-languages"></a>新しい言語

Translator 用の新しい言語システムの作成に取り組んでいるコミュニティや組織のお客様は、[custommt@microsoft.com](mailto:custommt@microsoft.com) までご連絡いただき、詳細についてお問い合わせください。

## <a name="next-steps"></a>次のステップ

- [モデルをトレーニングします](how-to-train-model.md)。
- [Translator V3](../reference/v3-0-translate.md?tabs=curl) を介してデプロイされた翻訳モデルを使い始めます。