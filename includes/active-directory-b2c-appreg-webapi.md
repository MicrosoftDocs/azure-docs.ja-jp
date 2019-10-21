---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720268"
---
アクセス トークンを提示するクライアント アプリケーションによる保護されたリソース要求を Web API リソースで受け取って処理できるためには、Web API リソースをテナントに登録しておく必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
1. **[Web アプリ/Web API]** には、 **[はい]** を選択します。
1. **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
1. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 このチュートリアルでは、サンプルはローカル環境で実行され、`https://localhost:5000` でリッスンします。
1. **[アプリケーション ID/URI]** には、表示されている URI に対する API エンドポイント識別子を追加します。 このチュートリアルでは、完全な URI が `https://contosob2c.onmicrosoft.com/api` のような形になるように、「`api`」と入力します。
1. **作成** を選択します。
1. 後の手順で使用するために、**アプリケーション ID** を記録しておきます。
