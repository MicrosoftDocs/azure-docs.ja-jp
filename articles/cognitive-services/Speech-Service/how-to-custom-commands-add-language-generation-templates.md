---
title: 方法:音声応答の言語生成テンプレートを使用する - Speech サービス
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドで言語生成テンプレートを使用する方法について説明します。 言語生成テンプレートを使用すると、クライアントに送信される応答をカスタマイズし、応答にバリエーションを導入することができます。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307337"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>音声応答の言語生成テンプレートを追加する

この記事では、カスタム コマンドで言語生成テンプレートを使用する方法について説明します。 言語生成テンプレートを使用すると、クライアントに送信される応答をカスタマイズし、応答にバリエーションを導入することができます。 言語生成のカスタマイズは、次の方法で行うことができます。

- 言語生成テンプレートを使用します
- アダプティブ式を使用します

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。

> [!div class="checklist"]
> * [方法: 単純なコマンドを使用してアプリケーションを作成する](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [方法: コマンドにパラメーターを追加する](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>言語生成テンプレートの概要

カスタム コマンド テンプレートは、BotFramework の [LG テンプレート](https://aka.ms/speech/cc-lg-format)に基づいています。 カスタム コマンドでは、必要に応じて新しい LG テンプレートが作成されるため (つまり、パラメーターまたはアクションでの音声応答の場合)、LG テンプレートの名前を指定する必要はありません。 したがって、テンプレートを次のようには定義しません。

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

必要なのは、次のように、名前を指定せずにテンプレートの本体を定義することだけです。

> [!div class="mx-imgBorder"]
> ![テンプレート エディターの例](./media/custom-commands/template-editor-example.png)


この変更により、クライアントに送信される音声応答にバリエーションが導入されます。 そのため、同じ発話に対して、対応する音声応答は、指定されたオプションからランダムに選択されます。

LG テンプレートを利用すると、アダプティブ式を使用してコマンドに複雑な音声応答を定義することもできます。 詳細については、[LG テンプレートの形式](https://aka.ms/speech/cc-lg-format)に関するページを参照してください。 既定のカスタム コマンドでは、次のようなわずかな違いはありますが、すべての機能がサポートされています。

* LG テンプレートのエンティティは、${entityName} と表されます。 カスタム コマンドでは、エンティティは使用しませんが、${parameterName} または {parameterName} という表現のいずれかを使用して、変数として使用することができます
* テンプレートの構成と拡張は、カスタム コマンドではサポートされていません。 これは、`.lg` ファイルを直接編集するのではなく、自動的に作成されたテンプレートの応答のみを編集するためです。
* LG によって挿入されるカスタム関数は、カスタム コマンドではサポートされていません。 定義済みの関数は同じようにサポートされます。
* オプション (具体的には、replaceNull と lineBreakStyle) は、カスタム コマンドではサポートされていません。

## <a name="add-template-responses-to-turnonoff-command"></a>TurnOnOff コマンドにテンプレート応答を追加する

**TurnOnOff** コマンドを変更し、次の構成で新しいパラメーターを追加します。

| 設定            | 推奨値       | 
| ------------------ | --------------------- | 
| Name\(名前\)               | `SubjectContext`         | 
| Is Global          | チェック解除             | 
| Required\(必須\)           | チェック解除               | 
| Type               | String                |
| Default value\(既定値\)      | `all` |
| Configuration\(構成\)      | 内部カタログから事前定義済み入力値を受け取る | 
| Predefined input values\(定義済み入力値\) | `room`、`bathroom`、`all`|

### <a name="modify-completion-rule"></a>完了ルールを変更する

既存の完了ルール **ConfirmationResponse** の **[アクション]** セクションを変更します。 **[アクションの編集]** ポップアップで、 **[Template Editor]\(テンプレート エディター\)** に切り替えて、テキストを次の例に置き換えます。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

次のようにアプリケーションを **[トレーニング]** して **[テスト]** します。 テンプレート値の複数の代替値の使用とアダプティブ式の使用による応答のバリエーションに注意してください。

* 入力: turn on the tv (テレビをつけて)
* 出力:Ok, turning the tv on (はい、テレビをつけます)
* 入力: turn on the tv (テレビをつけて)
* 出力:Done, turned on the tv (テレビをつけました)
* 入力: turn off the lights (ライトを消して)
* 出力:Ok, turning all the lights off (はい、すべてのライトをオフにします)
* 入力: turn off room lights (部屋のライトを消して)
* 出力:Ok, turning off the room lights (はい、部屋のライトをオフにします)

## <a name="use-custom-voice"></a>Custom Voice を使用する

カスタム コマンドの応答をカスタマイズするもう 1 つの方法は、カスタム出力音声を選択することです。 既定の音声をカスタム音声に切り替えるには、次の手順のようにします。

1. カスタム コマンド アプリケーションで、左側のペインの **[設定]** を選択します。
1. 中央のペインで **[Custom Voice]** を選択します
1. テーブルから目的のカスタムまたはパブリック音声を選択します
1. **[保存]** を選択します。

> [!div class="mx-imgBorder"]
> ![例文とパラメーター](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - **パブリック音声**の場合、**ニューラル型**は特定のリージョンでのみ使用できます。 使用できるかどうかを確認するには、[リージョンまたはエンドポイントによる標準およびニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)に関するページを参照してください。
> - **カスタム音声**については、Custom Voice プロジェクト ページから作成できます。 「[Custom Voice の概要](./how-to-custom-voice.md)」を参照してください。

これで、アプリケーションは既定の音声ではなく、選択した音声で応答します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK を使用してカスタム コマンドを統合します](./how-to-custom-commands-setup-speech-sdk.md)。
