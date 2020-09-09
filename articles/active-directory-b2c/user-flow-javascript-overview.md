---
title: JavaScript とページ レイアウトのバージョン
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で JavaScript を有効にし、ページ レイアウトのバージョンを使用する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-javascript
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 58ddca6bb7844288aae0370f035bc4dfa2dc942f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481685"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C における JavaScript とページ レイアウトのバージョン

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C では、ユーザー フローとカスタム ポリシーのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むパッケージ コンテンツのセットが提供されています。

アプリケーションで JavaScript を有効にするには、次の手順を実行します。

* Azure portal を使用して、ユーザー フローで有効にする
* [ページ レイアウト](page-layout.md)を選択する
* 要求で [b2clogin.com](b2clogin.md) を使用する

[JavaScript](javascript-samples.md) クライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 変更不可になっていないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 これらの問題を回避するには、ページ レイアウトの使用を強制し、ページ レイアウト バージョンを指定して、JavaScript の基になっているコンテンツ定義を確実に不変にします。 JavaScript を有効にする予定がない場合でも、ページに対してページ レイアウトのバージョンを指定できます。

## <a name="enable-javascript"></a>JavaScript を有効にする

ユーザー フローの **[プロパティ]** で、JavaScript を有効にすることができます。 JavaScript を有効にすると、ページ レイアウトの使用も強制されます。 その後、次のセクションで説明するように、ユーザー フローのページ レイアウトのバージョンを設定できます。

![JavaScript の有効化設定を強調したユーザー フローのプロパティ ページ](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>ページ レイアウトのバージョンを選択する

ユーザー フローのプロパティで JavaScript を有効にするかどうかに関係なく、ユーザー フローのページに対するページ レイアウトのバージョンを指定できます。 ユーザー フローを開き、 **[ページ レイアウト]** を選択します。 **[レイアウト名]** で、ユーザー フローのページを選択して、 **[ページ レイアウト バージョン (プレビュー)]** を選択します。

さまざまなページ レイアウトのバージョンの詳細については、[ページ レイアウトのバージョン変更ログ](page-layout.md)に関する記事を参照してください。

![ポータルでのページ レイアウト設定でページ レイアウトのバージョンのドロップダウン リストを表示した状態](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>次のステップ

JavaScript の使用例については、「[Azure Active Directory B2C で使用するための JavaScript のサンプル](javascript-samples.md)」を参照してください。
