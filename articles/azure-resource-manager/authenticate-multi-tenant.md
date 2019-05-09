---
title: テナントをまたいだ認証 - Azure Resource Manager
description: Azure Resource Manager がテナントをまたいだ認証要求を処理する方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109477"
---
# <a name="authenticate-requests-across-tenants"></a>テナントをまたいだ認証要求

マルチ テナント アプリケーションを作成するとき、異なるテナントに含まれるリソースの認証要求を処理することが必要な場合があります。 一般的なシナリオとして、1 つのテナント内の仮想マシンが、別のテナント内の仮想ネットワークに参加しなければならないことがあります。 Azure Resource Manager には、別のテナントへの要求を認証する補助トークンを格納するためのヘッダー値が用意されています。

## <a name="header-values-for-authentication"></a>認証のためのヘッダー値

要求では、次の認証ヘッダー値があります。

| ヘッダー名 | 説明 | 値の例 |
| ----------- | ----------- | ------------ |
| Authorization | プライマリ トークン | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 補助トークン | Bearer &lt;auxiliary-token1&gt;; EncryptedBearer &lt;auxiliary-token2&gt;; Bearer &lt;auxiliary-token3&gt; |

補助ヘッダーは最大 3 つの補助トークンを保持できます。 

マルチ テナント アプリのコードでは、他のテナントの認証トークンを取得し、補助ヘッダー内に格納します。 すべてのトークンは、同じユーザーまたはアプリケーションからのものである必要があります。 ユーザーまたはアプリケーションは、ゲストとして他のテナントに招待されたことがある必要があります。

## <a name="processing-the-request"></a>要求の処理

アプリが Resource Manager に要求を送信すると、要求はプライマリ トークンの ID で実行されます。 プライマリ トークンは、期限が切れていない有効なものでなければなりません。 このトークンは、サブスクリプションを管理できるテナントからのものである必要があります。

要求が別のテナントからのリソースを参照するとき、Resource Manager は補助トークンをチェックして、要求を処理できるかどうかを判断します。 ヘッダー内のすべての補助トークンは、期限が切れていない有効なものでなければなりません。 いずれかのトークンが期限切れの場合、Resource Manager は 401 応答コードを返します。 応答には、無効なトークンからのクライアント ID およびテナント ID が含まれます。 補助ヘッダーには、テナントをまたぐ要求が処理される、テナントに対する有効なトークンが含まれています。

## <a name="next-steps"></a>次の手順
* Azure Resource Manager API を使用した認証要求の送信の詳細については、「[サブスクリプションにアクセスするための Resource Manager 認証 API の使用](resource-manager-api-authentication.md)」を参照してください。
* トークンの詳細については、「[Azure Active Directory アクセス トークン](/azure/active-directory/develop/access-tokens)」を参照してください。
