---
title: 新しい Service Fabric クラスターのマネージド ID のサポートを構成する
description: 新しい Azure Service Fabric クラスターでマネージド ID のサポートを有効にする方法を次に示します
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574668"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>新しい Service Fabric クラスターのマネージド ID のサポートを構成する

Service Fabric アプリケーションで [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用するには、まずクラスターで *マネージド ID トークン サービス* を有効にします。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、他のシステム サービスの横で **fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

> [!NOTE]
> **マネージド ID トークン サービス** を有効にするには、Service Fabric ランタイム バージョン 6.5.658.9590 以降が必要です。  

## <a name="enable-the-managed-identity-token-service"></a>マネージド ID トークン サービスを有効にする

クラスターの作成時にマネージド ID トークン サービスを有効にするには、クラスターの Azure Resource Manager テンプレートに次のスニペットを追加します。

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>エラー

このメッセージでデプロイが失敗する場合は、クラスターが、必要な Service Fabric バージョン (サポートされている最小ランタイムは 6.5 CU2) になっていないことを意味します。


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>関連記事

* Azure Service Fabric での[マネージド ID のサポート](./concepts-managed-identity.md)を確認します

* [既存の Azure Service Fabric クラスターでマネージド ID のサポートを有効にする](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>次のステップ

* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する](./how-to-grant-access-other-resources.md)