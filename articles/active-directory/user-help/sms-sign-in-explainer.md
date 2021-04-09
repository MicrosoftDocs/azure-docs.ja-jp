---
title: 電話番号の SMS サインイン ユーザー エクスペリエンス - Azure AD
description: 新規または既存の電話番号での SMS サインイン ユーザー エクスペリエンスについて詳しく説明します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/21/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 1a50f2032a978a552205d1bba602249f34f0478a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661596"
---
# <a name="use-your-phone-number-as-a-user-name"></a>ユーザー名として電話番号を使用する

デバイスを登録すると、ご自身の電話から組織のサービスにアクセスできるようになります。また、組織からご自身の電話へのアクセスは許可されません。 ご自身が管理者の場合は、[SMS ベース認証に対するユーザーの構成と有効化](../authentication/howto-authentication-sms-signin.md)に関する記事で詳細を確認できます。

所属する組織が SMS サインインを利用可能にしていない場合、アカウントに電話を登録しても、そのオプションは表示されません。  

## <a name="when-you-have-a-new-phone-number"></a>新しい電話番号がある場合

新しい電話または新しい電話番号を入手し、SMS サインインが利用可能な組織でそれを登録すると、通常の電話登録のプロセスが開始されます。

1. **[メソッドの追加]** を選択します。
1. **[電話]** を選択します。
1. 電話番号を入力し、 **[コードを SMS 送信する]** を選択します。
1. コードを入力し終えたら、 **[次へ]** を選択します。
1. "SMS が検証されました。 お使いの電話が正常に登録されました" というプロンプトが表示されます。

> [!Important]
> 既知の問題が原因で、しばらくの間は、電話番号を追加しても SMS サインイン用にその番号が登録されません。 追加した番号を使用してサインインしてから、プロンプトに従って SMS サインイン用に番号を登録する必要があります。

### <a name="when-the-phone-number-is-in-use"></a>電話番号が使用中の場合

組織内の他のユーザーが使っている電話番号を使用しようとすると、次のメッセージが表示されます。

![電話番号が既に使用されている場合のエラー メッセージ](media/sms-sign-in-explainer/sms-sign-in-error.png)

管理者に連絡して問題を修復してください。

## <a name="when-you-have-an-existing-number"></a>既存の番号がある場合

ある電話番号を既に組織で使用していて、電話番号のユーザー名としての使用が利用可能になった場合、次の手順に従ってサインインすることができます。

1. SMS サインインが利用可能になると、SMS サインイン用に電話番号を有効にするかどうかを確認するバナーが表示されます。

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="電話番号への SMS サインインを有効にするためのバナーを示すスクリーンショット。[有効化] 操作が選択されています。" lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. また、電話の方法のタイルにあるキャレットを選択すると、 **[有効化]** ボタンが表示されます。

    [![電話番号での SMS サインインを有効化するためのバナー。](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 方法を有効にするには、 **[有効化]** を選択します。 操作の確認を求めるメッセージが表示されます。

    ![電話番号での SMS サインインを有効化するための確認ダイアログ](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. **[有効化]** を選択します。

## <a name="when-you-remove-your-phone-number"></a>電話番号を削除する場合

1. 電話番号を削除するには、SMS サインインの電話の方法のタイルにある削除ボタンを選択します。

    [![電話番号での SMS サインインを削除するためのバナー。](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 操作の確認を求めるメッセージが表示されたら、 **[OK]** を選択します。

既定のサインイン方法として使用されている電話番号を削除することはできません。 番号を削除するには、既定のサインイン方法を変更してから、もう一度電話番号を削除する必要があります。
