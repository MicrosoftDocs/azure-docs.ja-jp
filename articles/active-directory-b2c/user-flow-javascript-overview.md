---
title: Azure Active Directory B2C でのユーザー フローに対する JavaScript とページ コントラクトのバージョン | Microsoft Docs
description: Azure Active Directory B2C で JavaScript を有効にし、ページ コントラクトのバージョンを使用してユーザー フローをカスタマイズする方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401558"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>ユーザー フローで JavaScript とページ コントラクトのバージョンを使用する方法

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C では、ユーザー フローのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むパッケージ コンテンツのセットが提供されています。 ユーザー フローで [JavaScript](javascript-samples.md) のクライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 そうしないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 このような問題を防ぐには、ユーザー フローに対してページ コントラクトの使用を強制して、ページ コントラクトのバージョンを指定することができます。 このようにすると、JavaScript の基になるすべてのコンテンツ定義が変更不可になります。 ユーザー フローで JavaScript を有効にする予定がない場合でも、ユーザー フローのページに対してページ コントラクトのバージョンを指定できます。

> [!NOTE]
> この記事では、ユーザー フローに対する JavaScript について説明しますが、[カスタム ポリシー](page-contract.md)を使用するときも、JavaScript を使用して、ページ コントラクトのバージョンを選択することができます。

## <a name="enable-javascript"></a>JavaScript を有効にする

ユーザー フローのプロパティで、JavaScript を有効にできます。それにより、ページ コントラクトの使用も強制されるようになります。 その後、次のセクションで説明するように、ページ コントラクトのバージョンを設定することができます。

![JavaScript を有効にする設定](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>ページ コントラクトのバージョンを指定する

ユーザー フローのプロパティで JavaScript を有効にするかどうかに関係なく、ユーザー フローのページに対するページ コントラクトのバージョンを指定できます。 ユーザー フローを開き、**[ページ レイアウト]** を選択します。 **[レイアウト名]** で、ユーザー フローのページを選択して、**[契約バージョンのページ]** を選択します。

![JavaScript を有効にする設定](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>次の手順
「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」をご覧ください。
