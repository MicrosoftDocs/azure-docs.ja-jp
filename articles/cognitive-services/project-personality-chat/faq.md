---
title: よく寄せられる質問 - Personality Chat
titlesuffix: Azure Cognitive Services
description: Personality Chat の FAQ
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 88dfbb2a547b44de1bb98ca536c7841570a12168
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766837"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>他のチャット ボットのようにすべての質問に回答しないのはなぜですか。

Project Personality チャットは、パーソナリティを紹介し、より完全なユーザー エクスペリエンスを作成する共通の短い会話でボットを強化します。 ボットの主な機能に関連しないトピックに関する長い会話を実行するように設計されていません。 すべての会話に応答できますが、ベータ版では、一般的な短い会話のドメインに制限されています。

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>自分のブランドに合わせてパーソナリティをカスタマイズする方法を教えてください。

使用可能な既定のペルソナから最も近いペルソナを選択します。 現在、エディトリアル ライブラリを使用し、組織のブランドに合わせて応答を編集できます。 将来的には、選択したパーソナリティからサンプルの発話のセットをアップロードし、最も近いペルソナ ID のバージョンを見つけることができます。 モデルを再トレーニングしてカスタマイズする方法もあります。

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>このサービスでは、Zo などの既存のインテリジェントなエージェントが強化されますか。

Zo、Cortana、および Project Personality Chat をサポートするサービスは、いくつかの同じ手法を共有しますが、スタックは異なります。 これには、Zo と Cortana の経験から学習内容が組み込まれます。

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>このサービスは、悪いカスタマー エクスペリエンスにつながる可能性がありますか。

豊富なエクスペリエンスを提供するために、Personality Chat は、エディトリアル データ・セットのもの以外の応答を生成することができ、すべてのユーザー入力を解釈しようとします。 そのため、応答がコンテキストで正しくない可能性があります。 好ましくない応答を回避できるようにいくつかのコントロールが組み込まれており、それらは、Zo などのインテリジェントなエージェントの知識の上に構築されています。 既定では、Project Personality Chat は、認識したユーザー意図にのみ応答するように設定されています。 Project Personality Chat が状況に適しているかどうかをテストすることができます。 さらにトレーニングが必要なものがある場合は、フィードバックをお送りください。 将来的にお客様とこのサービスを使用する場合、ライブのユーザーとのやりとりの問題を特定できるように、匿名のロギングを検討することをお勧めします。
