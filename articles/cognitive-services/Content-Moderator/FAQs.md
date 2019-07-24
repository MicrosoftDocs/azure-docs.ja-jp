---
title: よく寄せられる質問 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator に関してよく寄せられる質問とその回答を紹介しています。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351135"
---
# <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

### <a name="what-does-my-content-moderator-subscription-include"></a>Content Moderator のサブスクリプションには何が含まれていますか。

Content Moderator のサブスクリプションには、レビュー ツールと API へのアクセスが含まれています。 ビジネス ニーズに応じて、どちらを利用するか、両方を利用するかを決定できます。

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>API を使用してモデレート可能なコンテンツに対する制限/制約はどのようなものですか。

API を使用する場合、画像のピクセル数は最大 128 ピクセル、ファイル サイズは 4 MB までにする必要があります。 テキストの長さは最大 1024 文字にできます 動画の長さに制限はありません。

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Text API または Image API に渡したコンテンツがサイズ制限を超えるとどうなりますか。

Text API から、テキストが上限を超過していることを示すエラー コードが返されます。 同様に、Image API からは、画像がサイズ要件を満たしていないことを示すエラー コードが返されます。

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>モデレーションのために送信された画像、テキスト、動画は保存されますか。

お客様のコンテンツはお客様自身のものであり、同意なくマイクロソフトが保持することはできません。 マイクロソフトでは、お客様のコンテンツを保護するために業界をリードするセキュリティ対策を使用しています。

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Content Moderator を使用し、子どもの性的搾取に該当する違法な画像を審査することはできますか。

いいえ。 ただし、認められた機関は [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") を使用してこのようなコンテンツを審査できます。

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>いくつのレビュー チームがユーザー参加を利用できますか。 ユーザーはチームを切り替えることができますか。

ユーザーは一度に 1 つのチームにのみ参加できます。

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>レビュー ツールでサポートされるチーム メンバーの役割はどのような種類ですか。 これらはどのように違うのでしょうか。

レビュー ツールでは、現在管理者とレビュー担当者の役割を割り当てることができます。 管理者は他のユーザーを招待して、構成設定にアクセスできます。レビュー担当者が行えるのは、モデレーション結果の確認と、コンテンツのタグ付けやタグ解除のみです。

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>タグとは何ですか。 レビュー ツールではカスタム タグはサポートされますか。

タグとは、成人向け画像が 'a'、きわどい画像が 'r' など、モデレーション フラグを示す 1 文字または 2 文字の短いコードです。 管理者は、必要に応じて業務のために新しいタグを定義できます。

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>レビュー チームに設定できるチーム メンバーは何人ですか。

チームには、管理者も含めて最大 5 人のチーム メンバーを入れることができます。
