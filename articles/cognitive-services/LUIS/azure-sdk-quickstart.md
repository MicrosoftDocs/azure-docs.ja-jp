---
title: クイック スタート:Language Understanding (LUIS) SDK クライアント ライブラリ
description: C#、Python、または JavaScript を使用したこのクイックスタートでは、LUIS SDK クライアント ライブラリを使用して LUIS アプリを作成し、クエリを実行します。
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, 人工知能, ai, 自然言語処理, nlp, 自然言語理解, nlu, ai 会話, 会話型 ai, ai チャットボット, チャットボット メーカー, LUIS, nlp ai, luis ai, azure luis, 自然言語の理解
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323085"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>クイック スタート:LUIS アプリを作成して照会するための Language Understanding (LUIS) SDK クライアント ライブラリ

C#、Python、または JavaScript を使用したこのクイックスタートでは、LUIS SDK クライアント ライブラリを使用して LUIS アプリを作成し、クエリを実行します。

Language Understanding (LUIS) を使うと、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。

* **作成 SDK** クライアント ライブラリを使用すると、LUIS アプリを作成、編集、トレーニング、および公開できます。 * **予測ランタイム SDK** クライアント ライブラリを使用すると、公開されているアプリに対してクエリを実行できます。

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[LUIS ポータル](https://www.luis.ai)からアプリを削除し、[Azure portal](https://portal.azure.com/) から Azure リソースを削除することができます。

## <a name="troubleshooting"></a>トラブルシューティング

* クライアント ライブラリへの認証 - 認証エラーは通常、間違ったキーとエンドポイントが使用されたことを示します。 このクイックスタートでは、便宜上、予測ランタイムの作成キーとエンドポイントを使用しますが、これは月間クォータをまだ使っていない場合にのみ機能します。 作成キーとエンドポイントを使用できない場合は、予測ランタイム SDK クライアント ライブラリにアクセスするときに予測ランタイム キーとエンドポイントを使用する必要があります。
* エンティティの作成 - このチュートリアルで使用されている入れ子になった機械学習エンティティの作成でエラーが発生した場合は、コードをコピーした後で別のエンティティを作成するようにコードを変更していないことを確認してください。
* 発話の例の作成 - このチュートリアルで使用されているラベル付きの発話の例の作成でエラーが発生した場合は、コードをコピーした後で別のラベル付きの例を作成するようにコードを変更していないことを確認してください。
* トレーニング - トレーニング エラーが発生した場合、これは通常、(発話の例が含まれる意図がない) 空のアプリ、または無効な形式の意図またはエンティティが含まれるアプリを示します。
* その他のエラー - コードではテキストおよび JSON オブジェクトを使用してクライアント ライブラリを呼び出すため、コードを変更していないことを確認してください。

これ以外のエラー - 上記の一覧に記載されていないエラーが発生した場合は、このページの下部でフィードバックを送信してお知らせください。 インストールしたクライアント ライブラリのプログラミング言語とバージョンを含めます。 

## <a name="next-steps"></a>次の手順

* [Language Understanding (LUIS) API とは](what-is-luis.md)
* [新機能](whats-new.md)
* [意図](luis-concept-intent.md)、[エンティティ](luis-concept-entity-types.md)、[発話の例](luis-concept-utterance.md)、[事前構築済みエンティティ](luis-reference-prebuilt-entities.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code) にあります。
