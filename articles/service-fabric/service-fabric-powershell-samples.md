---
title: Azure PowerShell のサンプル - Service Fabric
description: Powershell を使用した Azure Service Fabric クラスター、アプリ、サービスの作成と管理について説明します。
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86187100"
---
# <a name="azure-service-fabric-powershell-samples"></a>Azure Service Fabric PowerShell のサンプル

次の表には、Service Fabric クラスター、アプリケーション、およびサービスを作成して管理する PowerShell のサンプル スクリプトへのリンクが含まれています。

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| スクリプト | 説明 |
|-|-|
| **クラスターの作成** ||
| [クラスターの作成 (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Azure Service Fabric クラスターを作成します。 |
| **クラスター、ノード、およびインフラストラクチャの管理** ||
| [アプリケーション証明書の追加](./scripts/service-fabric-powershell-add-application-certificate.md)| Key Vault に X509 証明書を作成して、クラスター内の仮想マシン スケール セットにデプロイします。 |
| [クラスター VM の RDP ポート範囲の更新](./scripts/service-fabric-powershell-change-rdp-port-range.md)|展開されたクラスター内にあるクラスター ノード VM の RDP ポート範囲を変更します。|
| [クラスター ノード VM の管理者ユーザー名とパスワードの更新](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | クラスター ノード VM の管理者ユーザー名とパスワードを更新します。 |
| [ロード バランサーでポートを開く](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Azure ロード バランサーの特定のポートで受信トラフィックを許可するため、アプリケーション ポートを開きます。 |
| [受信ネットワーク セキュリティ グループ ルールの作成](./scripts/service-fabric-powershell-add-nsg-rule.md) | 特定のポートで、クラスターへの着信トラフィックを許可する受信ネットワーク セキュリティ グループ ルールを作成します。 |
| **アプリケーションの管理** ||
| [アプリケーションをデプロイする](./scripts/service-fabric-powershell-deploy-application.md)| クラスターにアプリケーションをデプロイします。|
| [アプリケーションのアップグレード](./scripts/service-fabric-powershell-upgrade-application.md)| アプリケーションをアップグレードします。|
| [アプリケーションの削除](./scripts/service-fabric-powershell-remove-application.md)| クラスターからアプリケーションを削除します。|
