---
title: Conversation Learner モデルで ENUM エンティティと SET ENTITY アクションを使用する場合 - Azure Cognitive Services | Japanese Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで ENUM エンティティと SET ENTITY アクションを使用することが適切な場合について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ms.openlocfilehash: ed18d30a0c3f5d51cb3a07b8948863cdda16c1ae
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845965"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>ENUM エンティティと SET ENTITY アクションを使用する場合

このチュートリアルでは、ENUM (列挙型) エンティティと SET_ENTITY アクションを使用する方がよい場合について説明します。

## <a name="video"></a>ビデオ

[![Set Entity チュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>内容

このチュートリアルでは、2 つの新しい機能を紹介します。 ENUM (enumeration の略) という新しい種類のエンティティと、SET_ENTITY という新しい種類のアクションです。これらの列挙値のいずれかを参照し、名前が示すとおり、エンティティをこの値に設定することができます。 これから説明しますが、これらの新機能は一緒に使用されます。また、ここでは、それらの内容と使用方法についても説明します。 詳細に入る前に、これらの機能がどのような問題の解決に役立つかを理解しておくことが重要です。

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>問題点

会話の中で単語の意味が文脈に依存する場合があります。  通常、ラベル付きのキー ワードは言語理解サービスを使用して学習され、抽出されますが、このような場合、こうしたシステムではラベル付きの例を使用して学習できない場合があります。

たとえば、近くにいる人どうしの会話の一部を耳にして、"はい" という単語しか聞こえないとします。 その前にされた質問が聞こえなかったので、"はい" が何に対して同意または確認しているかがわかりません。 前にされた質問は文脈であり、答えに意味を与えます。 同様に、"はい" はさまざまな質問に対するとても一般的な答えなので、[カスタム トレーニング済み](04-introduction-to-entities.md)エンティティの場合のように例を提供しても学習できません。これは、個々の "はい" がそのエンティティとしてラベルを付けるように学習されるためです。

### <a name="example"></a>例

次の例を使ってさらにわかりやすく説明します。

ボット:Azure Cognitive Services は好きですか。
ユーザー:はい ボット:アイス クリームは好きですか。
ユーザー:はい

これまでのチュートリアルでは、[カスタム トレーニング済み](04-introduction-to-entities.md)エンティティに注目していました。まず考えるのは、"likesCogServices" という名前のエンティティを作成し、このエンティティとして最初の "はい" にラベルを付けることではないでしょうか。  ただし、このシステムでは 2 つ目の "はい" にもラベルが付けられます。 2 つ目の "はい" のラベルを "likesIceCream" に修正しようとすると、"はい" という 2 つの同じ入力が異なることを意味する競合が発生し、行き詰まります。

このような場合は、ENUM エンティティと SET_ENTITY アクションを使用する必要があります。

## <a name="when-to-use-enums-or-setentity-actions"></a>ENUM または SET_ENTITY アクションを使用する場合

ENUM エンティティと SET_ENTITY アクションを使用する場合を理解するには、次の規則を使用します。

- エンティティの検出または設定がコンテキストに依存している
- 考えられる値の数が固定である ("はい" と "いいえ" は 2 つの値です)

言い換えると、確認の質問など、常に "はい" または "いいえ" になるクローズエンド型のプロンプトには、これらを使用します。

> [!NOTE]
> 現在、ENUM エンティティあたり最大 5 つの値という制限があります。 各値は、現在の 64 個の制限内のスロットの 1 つを使用します。 [cl-values-and-boundaries](../cl-values-and-boundaries.md) のページを参照してください。

例:ボット:ご注文は正しいですか。
ユーザー:はい

エンティティの考えられる値がオープンエンド型で固定されていない場合は、"[期待されるエンティティ](05-expected-entity.md)" などの代替機能を使用する必要があります。

例:ボット:お名前は何ですか。
ユーザー:マットです ボット:好きな色は何ですか。
ユーザー:シルバー

これらのプロンプトは、任意の値で答えられる可能性があるのでオープンエンド型と見なされます。

## <a name="what"></a>対象

### <a name="enum-entities"></a>ENUM エンティティ

ENUM エンティティの作成は、他のエンティティと同様です。 "プログラムによる" エンティティと同様に、これらのエンティティとして単語にラベルを付けることはできません。 代わりに、コードまたは SET_ENTITY アクションを介して設定する必要があります。

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Set Entity アクション

前述のように、"Set Entity" アクションは、エンティティを既知の列挙値に設定するだけです。 API コールバック アクションを作成し、メモリ マネージャーを使用してエンティティに値を設定しても、同じ結果が得られます。 例: `memory.Set(entityName, entityValue)` このコードを書いてこれらのアクションを作成する必要があることは面倒であり、管理が困難です。そのため、Conversation Learner には、この作業を容易にして、使用時にこれらのアクションを自動的に生成する特殊なアクションがあります。 これらを独立したアクションとして持つことで、ボット内で他のアクションやコードと組み合わせることなく、これらを構成することができます。

- Set Entity アクションは、ENUM エンティティの値を参照するときにのみ作成できるので、最初に ENUM エンティティを作成する必要があります。
- Set Entity アクションは "待機なし" アクションでもあります。これは、目に見える出力がなく、ユーザーが表示できる "待機" アクションでフォローアップする必要があるためです。
- Set Entity アクションは不変です。つまり、作成後に編集することはできません。

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>自動アクション生成

モデルに ENUM エンティティが存在する場合、Conversation Learner では考えられる各値に対してプレースホルダー アクションが作成され、それらがトレーニング中に選択できるようになります。 選択すると、アクションが自動的に作成されます。

たとえば、値が "はい" と "いいえ" の ENUM エンティティを作成したとします。

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

この新しい列挙型に対するアクションを明示的に作成しなくても、トレーニング中に使用できる 2 つの新しいアクションが表示されます。

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>これらの新機能を使ってボットを作成する

### <a name="requirements"></a>必要条件

このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

ここではファースト フードの注文をシミュレートするボットを作成します。 飲み物とフライド ポテトのサイズ (SMALL/MEDIUM/LARGE) の個別の値と、YES/NO の答えを持つ確認の質問があります。 これらのエンティティは両方とも、コンテキスト依存の答えと固定値であるという前述の 2 つの規則を満たしています。

### <a name="create-the-model"></a>モデルの作成

1. Web UI で [インポート] をクリックします。
2. "Tutorial-Enum-Set-Entity" という名前のチュートリアルを選択します。

これでモデルの管理ページに移動します。
モデルには既にいくつかの ENUM エンティティと Set Entity アクションが含まれています。

### <a name="create-the-first-dialog"></a>初めての会話を作成する

1. 左側のナビゲーション パネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. ユーザーが "Hi, I would like to order a coke and fries please" (こんにちは、コーラとフライド ポテトの注文をお願いします) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。

   > ユーザーは飲み物やフライド ポテトのサイズを指定していないので、それらを尋ねる必要があります。

4. 応答のあるアクションを選択します。"What size drink would you like?" (どのドリンクのサイズにしますか)
5. ユーザーは「large」(ラージ) と入力します。
6. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
7. アクション SET_ENTITY - "drinkSize:LARGE" を選択します
8. 応答のあるアクションを選択します。"What size fries would you like?" (どのフライド ポテトのサイズにしますか)
9. ユーザーは「Um, make those a medium」(ええと、ミディアムにします) と入力します。
10. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
11. アクション SET_ENTITY - "friesSize:MEDIUM" を選択します
12. 応答のあるアクションを選択します。"Would you like any condiments?" (調味料はいかがですか)
13. ユーザーは「Yes」(はい) と入力します
14. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
15. アクション SET_ENTITY - "condimentsConfirmation:YES" を選択します
16. 応答のあるアクションを選択します。"Ok, I have an order for a LARGE drink and MEDIUM fries. Is that correct?" (ラージのドリンク、ミディアムのフライド ポテトのご注文をいただきました。よろしいでしょうか)
17. ユーザーは「Yes」(はい) と入力します
18. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
19. アクション SET_ENTITY - "orderConfirmation:YES" を選択します
20. 応答のあるアクションを選択します。"Ok, your order number is 58. Please wait over there." (注文番号は 58 です。そちらでお待ちください)
21. [Save]\(保存\) をクリックしてダイアログを閉じます。

ENUM エンティティと SET_ENTITY アクションを使用して最初のダイアログを作成しました。 ユーザーが部分的な情報を指定したり、他の種類のクローズエンド型の質問に答えたりすることで、さらに多くの組み合わせを作成できます。

> [!NOTE]
> トレーニング中は、次のように SET_ENTITY アクションのプレースホルダーが表示されます。
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> ただし、ログ ダイアログの作成時や、デプロイされたボットの使用時には、これらは表示されません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [代替入力](./10-alternative-inputs.md)
