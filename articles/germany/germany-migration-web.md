---
title: Azure Web リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure Web リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 4e607d821d0a587b25c6cb6929ac6765d500b6fb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59490630"
---
# <a name="migrate-web-resources-to-global-azure"></a>Web リソースをグローバル Azure に移行する

この記事には、Azure Web リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="web-apps"></a>Web Apps

現時点では、Azure App Service の Web Apps 機能を使用して作成したアプリの Azure Germany からグローバル Azure への移行はサポートされていません。 Web アプリを Azure Resource Manager テンプレートとしてエクスポートすることをお勧めします。 次に、場所プロパティを新しい宛先リージョンに変更した後に、再デプロイします。

> [!IMPORTANT]
> 場所、Azure Key Vault シークレット、証明書、および、その他の GUID を新しいリージョンと一致するように、変更します。

詳細:

- [App Service のチュートリアル](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials)を完了することによって知識を更新します。
- [Azure Resource Manager テンプレートをエクスポートする](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)方法に関する情報を取得します。
- [Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)を確認します。
- [App Service の概要](../app-service/overview.md)を確認します。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を取得します。
- [テンプレートを再デプロイする](../azure-resource-manager/resource-group-template-deploy.md)方法を確認します。

## <a name="notification-hubs"></a>Notification Hubs

ある Azure Notification Hubs インスタンスから別のインスタンスに設定を移行するには、すべての登録トークンをそれらのタグと共に、エクスポートしてインポートします。

1. Azure Blob ストレージ コンテナーに[既存の通知ハブの登録をエクスポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))します。
1. ターゲット環境に新しい通知ハブを作成します。
1. BLOB ストレージから新しい通知ハブに、[登録トークンをインポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))します。

詳細:

- [Notification Hubs のチュートリアル](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Notification Hubs の概要](../notification-hubs/notification-hubs-push-notification-overview.md)を確認します。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
