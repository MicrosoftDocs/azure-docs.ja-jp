---
title: Azure Cloud Shell でリソースのマネージド ID を使用する
description: Azure Cloud Shell の MSI でコードを認証する
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469900"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Azure Cloud Shell で Azure リソースのマネージド ID を使用する

Azure Cloud Shell は、Azure リソースのマネージド ID による認可をサポートします。 これにより、アクセス トークンを取得して安全に Azure サービスと通信することができます。

## <a name="about-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID について
クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証に必要な資格情報を、コード上で安全に管理する方法です。 Cloud Shell では、スクリプトに必要な資格情報の Key Vault からの取得を認証する必要がある場合があります。

Azure リソースのマネージド ID は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

## <a name="acquire-access-token-in-cloud-shell"></a>Cloud Shell でアクセス トークンを取得する

次のコマンドを実行し、環境変数 `access_token` に MSI アクセス トークンを設定します。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>トークンの有効期限の処理

ローカル MSI サブシステムではトークンがキャッシュされます。 したがって、トークンは好きなだけ呼び出すことができます。また、次の場合にのみ、Azure AD の結果に対するネットワーク呼び出しが可能です。
- キャッシュにトークンがないため、キャッシュ ミスが発生した
- トークンの有効期限が切れている

コードでトークンをキャッシュする場合は、リソースがトークンの有効期限切れを示している場合のシナリオを処理できるよう準備する必要があります。

トークンのエラーを処理するには、[MSI アクセス トークンの使用に関する MSI ページ](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling)を参照してください。

## <a name="next-steps"></a>次のステップ
[MSI の詳細情報](../active-directory/managed-identities-azure-resources/overview.md)  
[MSI VM からアクセス トークンを取得する](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)