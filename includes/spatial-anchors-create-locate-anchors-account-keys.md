---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 72616d5d79b4dbdb8e14463c706c1626eeb50e97
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631671"
---
## <a name="set-up-authentication"></a>認証の設定

サービスにアクセスするには、アカウント キー、アクセス トークン、または Azure Active Directory 認証トークンを提供する必要があります。

### <a name="account-keys"></a>アカウント キー

アカウント キーは、アプリケーションが Azure Spatial Anchors サービスで認証できるようにするための資格情報です。 アカウント キーの使用目的は、すぐに開始できるようにサポートすることです。 特にアプリケーションの Azure Spatial Anchors との統合の開発フェーズ時などがこれに該当します。 そのようなものとして、開発中にクライアント アプリケーションにアカウント キーを埋め込んで使用できます。 開発の先のフェーズに進むときには、実稼働レベルであるか、アクセス トークンによりサポートされるか、または Azure Active Directory ベースのユーザー認証である認証メカニズムに移行することを強く推奨します。 開発のためにアカウント キーを取得するには、Azure Spatial Anchors アカウントにアクセスし、[キー] タブに移動します。
