---
title: Azure Service Fabric - 既存の Azure Service Fabric クラスターを構成してマネージド ID のサポートを有効にする | Microsoft Docs
description: この記事では、既存の Azure Service Fabric クラスターを構成してマネージド ID のサポートを有効にする方法について説明します
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: adc21358011454c8687998dc5d257052959b933b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640731"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>既存の Azure Service Fabric クラスターを構成してマネージド ID のサポートを有効にする (プレビュー)
Azure Service Fabric アプリケーションのマネージド ID 機能にアクセスするには、まずクラスターで**マネージド ID トークン サービス**を有効にする必要があります。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、**fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

> [!NOTE]
> **マネージド ID トークン サービス**を有効にするには、Service Fabric ランタイム バージョン 6.5.658.9590 以降が必要です。  
> 
> クラスターの Service Fabric バージョンを調べるには、Azure portal からクラスター リソースを開き、 **[Essentials]\(基本\)** セクションで **[Service Fabric バージョン]** プロパティを確認します。
> 
> クラスターが **[手動]** アップグレード モードの場合、最初に 6.5.658.9590 以降にアップグレードする必要があります。


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>既存のクラスターでマネージド ID トークン サービスを有効にする
既存のクラスターで Managed Identity Token Service を有効にするには、2 つの変更を指定してクラスターのアップグレードを開始する必要があります。具体的には、Managed Identity Token Service を有効化し、各ノードの再起動を要求します。 これを行うには、Azure Resource Manager テンプレートに次の 2 つのスニペットを追加します。

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

また、変更を有効にするには、アップグレード ポリシーを変更し、クラスターでアップグレードが進行するのに合わせて各ノードで Service Fabric ランタイムを強制的に再起動するよう指定する必要があります。 この再起動により、新たに有効になったシステム サービスが各ノードで確実に開始および実行されます。 次のスニペットでは、`forceRestart` が必須の設定です。残りの設定には既存の値を使用します。  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> アップグレードが正常に完了したら、`forceRestart` の設定をロールバックすることを忘れないでください。これにより、以降のアップグレードの影響を最小限に抑えることができます。 

## <a name="errors-and-troubleshooting"></a>エラーとトラブルシューティング

デプロイが失敗し、次のメッセージが表示された場合は、十分に新しいバージョンの Service Fabric がクラスターで実行されていないことを意味します。

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>次の手順
* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つアプリケーションのデプロイ方法](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセスを許可する](./how-to-grant-access-other-resources.md)