---
title: 事前構築済みのモデル
titleSuffix: Language Understanding - Azure Cognitive Services
description: 事前構築済みモデルでは、ドメイン、意図、発話、およびエンティティが提供されます。 事前構築済みドメインでアプリを始めることも、後でアプリに関連するドメインを追加することもできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 5d2ea9d971eff22ddeed4122c9697ca3096697b0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882847"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>事前構築済みのドメイン、意図、エンティティ モデル

事前構築済みモデルでは、ドメイン、意図、発話、およびエンティティが提供されます。 事前構築済みドメインでアプリを始めることも、後でアプリに関連するドメインを追加することもできます。 

## <a name="types-of-prebuilt-models"></a>事前構築済みモデルの種類

LUIS では、3 種類の事前構築済みモデルが提供されています。 各モデルは、いつでもアプリに追加できます。 

|モデルの種類|内容|
|--|--|
|Domain|意図、発話、エンティティ|
|意図|意図、発話|
|エンティティ|エンティティのみ| 

## <a name="prebuilt-domains"></a>事前構築済みドメイン

Language Understanding (LUIS) には*事前構築済みのドメイン*が用意されています。これは事前構築済みの一連の[意図](luis-how-to-add-intents.md)と[エンティティ](luis-concept-entity-types.md)で、クライアント アプリケーションのドメインまたは共通のカテゴリに対して連携して動作します。 

事前構築済みドメインはトレーニングされており、LUIS アプリに追加できる状態になっています。 アプリに追加した後、事前構築済みドメイン内の意図とエンティティは完全にカスタマイズ可能です。 

事前構築済みのドメイン全体からカスタマイズを開始する場合は、アプリに不要な意図とエンティティを削除します。 事前構築済みのドメインに既に用意されているセットに、意図またはエンティティをいくつか追加することもできます。 たとえば、スポーツ イベント アプリに対して**イベント**事前構築済みドメインを使用する場合は、スポーツ チームのエンティティを追加できます。 LUIS への[発話の提供](luis-how-to-add-example-utterances.md)を開始するときに、ご自身のアプリに固有の用語を追加します。 LUIS はその用語を認識して学習することで、事前構築済みのドメインの意図およびエンティティを、ご自身のアプリのニーズに合わせて調整します。 

> [!TIP]
> 事前構築済みのドメインの意図とエンティティは、組み合わせて使ったときに最も効果的に機能します。 意図とエンティティは、できるだけ同じドメインのものを組み合わせることをお勧めします。
> ユーティリティ事前構築済みドメインには、カスタマイズして任意のドメインで使用できる意図が含まれます。 たとえば、`Utilities.Repeat` をご自身のアプリに追加して、そのアプリケーションでユーザーが繰り返す必要がある任意のアクションが認識されるように、アプリをトレーニングできます。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>事前構築済みドメインの意図の動作変更

事前構築済みのドメインに含まれる意図の動作を変えて、LUIS アプリで使用したい場合があります。 たとえば、**場所**事前構築済みドメインに用意されている、レストランを予約するための `MakeReservation` 意図を、ご自身のアプリでは、ホテルを予約をする目的で使いたいと考えています。 この場合、その意図の動作を変更するには、ホテル予約に関する発話を LUIS に提供し、`MakeReservation` 意図を使用してその発話にラベルを付けます。これにより、ホテル予約の要求で `MakeReservation` 意図が認識されるように LUIS を再トレーニングできます。

[[Prebuilt domains reference]\(事前構築済みのドメインのリファレンス\)](./luis-reference-prebuilt-domains.md) では、事前構築済みのドメインの完全な一覧を確認できます。

## <a name="prebuilt-intents"></a>事前構築済み意図

LUIS では、事前構築済みの意図とその発話が提供されています。 ドメイン全体を追加することなく、意図を追加できます。 意図の追加は、意図とその発話を追加するプロセスです。 意図名と発話リストの両方を変更できます。  

## <a name="prebuilt-entities"></a>事前構築済みのエンティティ

LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 作成済みエンティティのサポートは、LUIS アプリのカルチャによって異なります。 カルチャによるサポートを含む、LUIS がサポートする作成済みエンティティの完全な一覧については、[作成済みエンティティのリファレンス](./luis-reference-prebuilt-entities.md)に関するページをご覧ください。

作成済みエンティティがアプリケーションに含まれる場合、エンティティの予測が発行されるアプリケーションに含まれます。 作成済みエンティティの動作は事前にトレーニングされており、変更することは**できません**。 作成済みエンティティの動作を確認するには、次の手順のようにします。

> [!NOTE]
> **builtin.datetime** は非推奨です。 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) によって置き換えられます。datetimeV2 は日付と時間の範囲を認識し、あいまいな日付と時刻の認識が強化されています。

## <a name="next-steps"></a>次の手順

アプリに[事前構築済みエンティティを追加する](luis-prebuilt-entities.md)方法を学習してください。
