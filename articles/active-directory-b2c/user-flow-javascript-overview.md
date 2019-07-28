---
title: JavaScript とページ コントラクトのバージョン - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C で JavaScript を有効にし、ページ コントラクトのバージョンを使用する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511980"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C における JavaScript とページ コントラクトのバージョン

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C では、ユーザー フローとカスタム ポリシーのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むパッケージ コンテンツのセットが提供されています。 アプリケーションで JavaScript を有効にするには、[カスタム ポリシー](active-directory-b2c-overview-custom.md)に要素を追加するかユーザー フローのポータルで有効にして、ページ コントラクトを選択して要求内に[b2clogin.com](b2clogin.md)を使用します。

[JavaScript](javascript-samples.md) のクライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 そうしないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 このような問題を防ぐには、ページ コントラクトの使用を強制して、ページ コントラクトのバージョンを指定することができます。 このようにすると、JavaScript の基になるすべてのコンテンツ定義が変更不可になります。 JavaScript を有効にする予定がない場合でも、ページに対してページ コントラクトのバージョンを指定できます。

## <a name="user-flows"></a>ユーザー フロー

ユーザー フローのプロパティで、JavaScript を有効にできます。それにより、ページ コントラクトの使用も強制されるようになります。 その後、次のセクションで説明するように、ページ コントラクトのバージョンを設定することができます。

![JavaScript を有効にする設定](media/user-flow-javascript-overview/javascript-settings.png)

ユーザー フローのプロパティで JavaScript を有効にするかどうかに関係なく、ユーザー フローのページに対するページ コントラクトのバージョンを指定できます。 ユーザー フローを開き、 **[ページ レイアウト]** を選択します。 **[レイアウト名]** で、ユーザー フローのページを選択して、 **[契約バージョンのページ]** を選択します。

![JavaScript を有効にする設定](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>カスタム ポリシー

カスタム ポリシーで JavaScript を有効にするには、カスタム ポリシー ファイル内で **RelyingParty** 要素に **ScriptExecution**要素を追加します。 詳細は、「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」をご覧ください。

カスタム ポリシーで JavaScript を有効にするかどうかに関係なく、ページに対するページ コントラクトのバージョンを指定できます。 ページのコントラクトを指定する方法については、「[Select a page contract in Azure Active Directory B2C using custom policies](page-contract.md)」 (カスタムポリシーを 使用した Azure Active Directory B2C におけるページ コンストラクトの選択) を参照してください。

## <a name="next-steps"></a>次の手順

「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」をご覧ください。
