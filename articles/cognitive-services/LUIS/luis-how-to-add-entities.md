---
title: 複数エンティティの追加
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) アプリでユーザーの発話から重要なデータを抽出するエンティティを作成します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0044cbc9e6142989a57e79de5fd1e78e999bb5e1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522352"
---
# <a name="create-entities-without-utterances"></a>発話なしでエンティティを作成する

エンティティは、抽出しようとしている発話内の単語またはフレーズを表します。 エンティティは、類似したオブジェクトのコレクション (場所、もの、人、イベント、または概念) を含むクラスを表します。 エンティティは、意図に関連する情報を説明し、アプリがタスクを実行するうえで不可欠なこともあります。 意図に発話を追加するとき、または意図への発話の追加とは別に (前または後)、エンティティを作成できます。

LUIS アプリ内のエンティティは、**[エンティティ]** ページの **[エンティティの一覧]** から追加、編集、削除できます。 LUIS で提供される主要なエンティティには、[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)と、独自の[カスタム エンティティ](luis-concept-entity-types.md#types-of-entities)の 2 種類があります。

機械学習済みエンティティが作成されたら、それが含まれているすべての意図のすべての発話例でそのエンティティをマークする必要があります。

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>事前構築済みエンティティをアプリに追加する

アプリケーションに追加される事前構築済みのエンティティで一般的なのは、*number* と *datetimeV2* です。 

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択します。
 
1. **[エンティティ]** ページで、**[事前構築済みエンティティを追加する]** を選択します。

1. **[事前構築済みエンティティを追加する]** ダイアログ ボックスで、事前構築済みのエンティティ **number** および **datetimeV2** を選択します。 **[完了]** を選択します。

    ![事前構築済みエンティティの追加ダイアログ ボックスのスクリーンショット](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>1 つの概念用に単純なエンティティを追加する

シンプル エンティティは 1 つの概念を示しています。 会社の部署名 (*人事*や*運営*など) を抽出するエンティティを作成するには、次の手順を使用します。   

1. アプリで、**[ビルド]** セクションを選択し、左パネル内の **[エンティティ]** を選択して、**[新しいエンティティの作成]** を選択します。

1. ポップアップ ダイアログ ボックスで、**[エンティティ名]** ボックスに「`Location`」と入力し、**[エンティティの種類]** リストから **[簡易]** を選択し、**[完了]** を選択します。

    このエンティティを作成したら、サンプル発話にそのエンティティが含まれている、すべての意図に移動します。 サンプル発話内のテキストを選択し、そのテキストをエンティティとしてマークします。 

    一般的には、シンプル エンティティのシグナルを強化するために[フレーズ リスト](luis-concept-feature.md)を使用します。

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>高度に構造化された概念のための正規表現エンティティを追加する

正規表現エンティティは、指定された正規表現に基づいて発話からデータを抽出するために使用されます。 

1. アプリで、左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

1. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Human resources form name`」と入力し、**[エンティティの種類]** リストから **[正規表現]** を選択し、正規表現「`hrf-[0-9]{6}`」を入力し、**[完了]** を選択します。 

    この正規表現は、リテラル文字 `hrf-` と一致します。それに続く 6 桁の数字は、人事フォームのフォーム番号を表します。

## <a name="add-hierarchical-entities"></a>階層構造エンティティを追加する

階層構造エンティティは、文脈から学習され、概念的に関連付けられる部類のエンティティです。 次の例では、エンティティに出発地と到着地が含まれます。 

発話 `Move John Smith from Seattle to Cairo` では、シアトルが出発地で、カイロが到着地です。 いずれの場所も文脈的に異なり、発話の中の単語の順序と選択から学習されます。

階層構造エンティティを追加するには、次の手順を完了します。 

1. アプリで、左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

1. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Location`」と入力し、**[エンティティの種類]** リストから **[Hierarchical]\(階層構造\)** を選択します。

    ![階層構造エンティティを追加する](./media/add-entities/hier-location-entity-creation.png)

1. **[子の追加]** を選択し、**[Child #1]\(子 #1\)** ボックスに「`Origin`」と入力します。 

1. **[子の追加]** を選択し、**[Child #2]\(子 #2\)** ボックスに「`Destination`」と入力します。 **[完了]** を選択します。

    >[!CAUTION]
    >子エンティティ名は、1 つのアプリのエンティティ全体で一意となる必要があります。 2 つの異なる階層構造エンティティには、同じ名前の子エンティティを含めることができません。 

    このエンティティを作成したら、サンプル発話にそのエンティティが含まれている、すべての意図に移動します。 サンプル発話内のテキストを選択し、そのテキストをエンティティとしてマークします。 

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>複合エンティティを追加して親子関係にグループ化する

複合エンティティを作成することによって、異なる種類のエンティティ間の関係を定義することができます。 次の例では、エンティティに正規表現が含まれていて、名前の事前構築済みエンティティも含まれています。  

発話 `Send hrf-123456 to John Smith` では、テキスト `hrf-123456` が人事部の[正規](#add-regular-expression-entities)と一致しており、事前構築済みエンティティ personName を使用して `John Smith` が抽出されます。 各エンティティは、より大きな親エンティティの一部です。 

1. アプリで、**[ビルド]** セクションの左側のナビゲーションから **[エンティティ]** を選択し、**[Add prebuilt entity]\(事前構築済みエンティティの追加\)** を選択します。

1. 事前構築済みエンティティ **PersonName** を追加します。 手順については、「[事前構築済みのエンティティを追加する](#add-prebuilt-entity)」を参照してください。 

1. 左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

1. ポップアップ ダイアログ ボックスで、**[エンティティ名]** ボックスに「`SendHrForm`」と入力し、**[エンティティの種類]** リストから **[Composite]\(複合\)** を選択します。

1. **[子の追加]** を選択して新しい子を追加します。

1. **[Child #1]\(子 #1\)** で、エンティティ **number** をリストから選択します。

1. **[Child #2]\(子 #2\)** で、一覧から **Human resources form name** エンティティを選択します。 

1. **[完了]** を選択します。

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>自由形式のエンティティをキャプチャする Pattern.any エンティティを追加する

[Pattern.any](luis-concept-entity-types.md) エンティティは[パターン](luis-how-to-model-intent-pattern.md)でのみ有効です (意図では無効です)。 このエンティティ タイプは、可変長のエンティティの終わりや単語の選択を LUIS が見つけやすくします。 このエンティティがパターンで使用されることにより、発話テンプレート内でエンティティの終わりがどこにあるかを LUIS が認識します。

アプリに `FindHumanResourcesForm` という意図がある場合、抽出されたフォーム タイトルが意図の予測に干渉する場合があります。 どの単語がフォーム タイトルに含まれているかを明確にするために、パターン内で Pattern.any を使用します。 LUIS の予測は発話によって始まります。 まず、発話がチェックされてエンティティと照合され、エンティティが見つかった場合、パターンがチェックおよび照合されます。 

「`Where is Request relocation from employee new to the company on the server?`」という発話で、フォーム タイトルは、タイトルがどこで終わり、発話の残りがどこから始まるか文脈上明らかでないため、扱いが困難です。 フォーム　タイトルについては、1 つの単語、句読点を含む複雑なフレーズ、無意味な単語の並びなど、あらゆる単語の順序が考えられます。 パターンを使用することで、完全で正確なエンティティを抽出できるエンティティを作成することができます。 タイトルが見つかると、`FindHumanResourcesForm` がパターンの意図であるため、この意図が予測されます。

1. **[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択し、**[新しいエンティティの作成]** を選択します。

1. **[エンティティの追加]** ダイアログ ボックスで、**[エンティティ名]** ボックスに `HumanResourcesFormTitle` と入力し、**[エンティティの種類]** として **[Pattern.any]** を選択します。

    Pattern.any エンティティを使用するには、(**[Improve app performance]\(アプリのパフォーマンス改善\)** セクションの) **[パターン]** ページで、「`Where is **{HumanResourcesFormTitle}** on the server?`」のように正しい中かっこ構文を使用してパターンを追加します。

    Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](luis-concept-patterns.md#explicit-lists)を使用してこの問題を修正します。 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>異なるコンテキストを区別するためにロールを追加する

ロールは、文脈に基づいた名前付きサブタイプです。 事前構築済みエンティティや非マシン学習エンティティを含む、すべてのエンティティで使用できます。 

出発地と到着地の階層構造エンティティと同じ例を使用した場合、違うのは、ロールが階層構造の子ではなく、名前付きの出発地であるという点です。 

ロールの構文は **{Entityname:Rolename}** で、エンティティ名、コロン、ロール名の順です。 たとえば、「 `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}` 」のように入力します。

1. **[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択します。

1. **[Create new entity]\(新しいエンティティの作成\)** を選択します。 `LocationUsingRoles` の名前を入力します。 **[簡易]** の型を選択して **[完了]** を選択します。 

1. 左側のパネルから **[エンティティ]** を選択し、前の手順で作成した新しいエンティティ **LocationUsingRoles** を選択します。

1. **[ロール名]** テキスト ボックスに、ロール `Origin` の名前を入力して Enter キーを押します。 `Destination` の 2 番目のロール名を追加します。 

    ![Location エンティティに Origin ロールを追加するスクリーンショット](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>完全一致のリスト エンティティを追加する

リスト エンティティとは、関連単語の集まりであり、固定かつ限定的です。 

人事管理アプリでは、すべての部署の一覧と共に、部署のシノニムを登録することができます。 エンティティを作成するとき、すべての値を知っている必要はありません。 実際にユーザーがシノニムで発話するのを見てから追加できます。

1. **[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択し、**[新しいエンティティの作成]** を選択します。

1. **[Add Entity]\(エンティティの追加\)** ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Department`」と入力し、**[エンティティの種類]** として **[リスト]** を選択します。 **[完了]** を選択します。
  
1. リスト エンティティのページでは、正規化された名前を追加できます。 **[値]** テキスト ボックスにリストの部署名 (`HumanResources` など) を入力し、キーボードの Enter を押します。 

1. 正規化された値の右側に、シノニムを入力し、各項目の入力後にキーボードの Enter を押します。

1. リストの正規化された項目がさらに必要な場合、**[Recommend]\(推奨\)** を選択して、[セマンティック辞書](luis-glossary.md#semantic-dictionary)からのオプションを表示します。

    ![[Recommend]\(推奨\) 機能を選択してオプションを表示する画面のスクリーンショット](./media/add-entities/hr-list-2.png)


1. 推奨リストの項目を選択して、その項目を正規化された値として追加するか、または **[すべて追加]** を選択してすべての項目を追加します。 
    次の JSON 形式を使用して、既存のリスト エンティティに値をインポートできます。

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>エンティティの種類は変更しないでください

エンティティを作成するために追加または削除するものを LUIS は認識していないため、LUIS ではエンティティの種類を変更することは認められていません。 種類を変更するには、正しい種類の新しいエンティティを少し違う名前で作成することをお勧めします。 エンティティが作成されたら、それぞれの発話内で、古いラベルのエンティティ名を削除して新しいエンティティ名を追加します。 すべての発話でラベルが書き換えられたら、古いエンティティを削除します。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>発話例からパターンを作成する

[予測の正確さを向上するためにパターンを追加する方法](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)に関するページをご覧ください。

## <a name="train-your-app-after-changing-model-with-entities"></a>エンティティを含むモデルの変更後にアプリをトレーニングする

エンティティを追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるようにアプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

事前構築済みエンティティについて詳しくは、[レコグナイザー テキスト](https://github.com/Microsoft/Recognizers-Text) プロジェクトをご覧ください。 

エンティティが JSON エンドポイント クエリの応答でどのように表示されるかについては、「[データの抽出](luis-concept-data-extraction.md)」をご覧ください

意図、発話、エンティティを追加したことで、基本的な LUIS アプリが完成しました。 アプリを[トレーニング](luis-how-to-train.md)、[テスト](luis-interactive-test.md)、および[発行](luis-how-to-publish-app.md)する方法を学びます。
 
