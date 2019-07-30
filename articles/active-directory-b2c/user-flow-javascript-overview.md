---
title: JavaScript とページ レイアウトのバージョン - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C で JavaScript を有効にし、ページ レイアウトのバージョンを使用する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227103"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C における JavaScript とページ レイアウトのバージョン

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C では、ユーザー フローとカスタム ポリシーのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むパッケージ コンテンツのセットが提供されています。 アプリケーションで JavaScript を有効にするには、[カスタム ポリシー](active-directory-b2c-overview-custom.md)に要素を追加するかユーザー フローのポータルで有効にして、ページ レイアウトを選択して要求内に[b2clogin.com](b2clogin.md)を使用します。

[JavaScript](javascript-samples.md) のクライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 そうしないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 このような問題を防ぐには、ページ レイアウトの使用を強制して、ページ レイアウトのバージョンを指定することができます。 このようにすると、JavaScript の基になるすべてのコンテンツ定義が変更不可になります。 JavaScript を有効にする予定がない場合でも、ページに対してページ レイアウトのバージョンを指定できます。

## <a name="user-flows"></a>ユーザー フロー

ユーザー フローの **[プロパティ]** で、JavaScript を有効にできます。それにより、ページ レイアウトの使用も強制されるようになります。 その後、次のセクションで説明するように、ユーザー フローのページ レイアウトのバージョンを設定できます。

![JavaScript の有効化設定を強調したユーザー フローのプロパティ ページ](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>ページ レイアウトのバージョンを選択する

ユーザー フローのプロパティで JavaScript を有効にするかどうかに関係なく、ユーザー フローのページに対するページ レイアウトのバージョンを指定できます。 ユーザー フローを開き、 **[ページ レイアウト]** を選択します。 **[LAYOUT NAME]\(レイアウト名\)** で、ユーザー フローのページを選択して、 **[Page Layout Version]\(ページ レイアウトのバージョン\)** を選択します。

さまざまなページ レイアウトのバージョンの詳細については、[バージョン変更ログ](page-layout.md#version-change-log)に関する項目を参照してください。

![ポータルでのページ レイアウト設定でページ レイアウトのバージョンのドロップダウン リストを表示した状態](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>カスタム ポリシー

カスタム ポリシーで JavaScript を有効にするには、カスタム ポリシー ファイル内で **RelyingParty** 要素に **ScriptExecution**要素を追加します。 詳細は、「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」をご覧ください。

カスタム ポリシーで JavaScript を有効にするかどうかに関係なく、ページに対するページ レイアウトのバージョンを指定できます。 ページ レイアウトを指定する方法については、[カスタムポリシーを使用した Azure Active Directory B2C でのページ レイアウトの選択](page-layout.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

さまざまなページ レイアウトのバージョンの詳細については、**カスタムポリシーを使用した Azure Active Directory B2C でのページ レイアウトの選択**に関する記事の、[バージョン変更ログ](page-layout.md#version-change-log)に関するセクションを参照してください。

JavaScript の使用例については、「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」を参照してください。
