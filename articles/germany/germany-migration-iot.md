---
title: Azure Germany からグローバル Azure に Azure IoT リソースを移行する
description: この記事では、Azure Germany からグローバル Azure への Azure IoT リソースの移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: ccb837001a4ef69ac6145e2f93bb9eb4766120f0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492133"
---
# <a name="migrate-iot-resources-to-global-azure"></a>IoT リソースをグローバル Azure に移行する

この記事には、Azure Germany からグローバル Azure への Azure IoT リソースの移行に役立つ情報が含まれています。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB データ移行ツールを使用して Azure Cosmos DB にデータを移行することができます。 Azure Cosmos DB データ移行ツールは、さまざまなソースからデータを Azure Cosmos DB にインポートするためのオープン ソース ソリューションです。

Azure Cosmos DB データ移行ツールは、グラフィカル インターフェイス ツールまたはコマンド ライン ツールとして使用できます。 ソース コードは、[Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub リポジトリで入手できます。 [ツールのコンパイル済みバージョン](https://www.microsoft.com/download/details.aspx?id=46436)は、Microsoft ダウンロード センターで入手できます。

Azure Cosmos DB リソースを移行するには、次の手順を完了することをお勧めします。

1. アプリケーションのアップタイム要件およびアカウント構成を確認して、最適なアクション プランを決定します。
1. データ移行ツールを実行して、Azure Germany から新しいリージョンにアカウント構成を複製します。
1. メンテナンス ウィンドウを使用できる場合は、データ移行ツールを実行することによって、ソースから宛先にデータをコピーします。
1. メンテナンス ウィンドウを使用できない場合は、ツールを実行してソースから宛先にデータをコピーしてから、次の手順を完了します。
   1. 構成主導のアプローチを使用して、アプリケーションでの読み取り/書き込みに変更を加えます。
   1. 初回の同期を完了します。
   1. 増分同期を設定し、変更フィードを反映します。
   1. 新しいアカウントの読み取りを指示し、アプリケーションを確認します。
   1. 古いアカウントへの書き込みを停止し、変更フィードが反映されていることを確認してから、新しいアカウントに対する書き込みを指示します。
   1. このツールを停止し、古いアカウントを削除します。
1. 新旧のアカウント間でデータが一貫性のあることを検証するツールを実行します。

詳細:

- [Azure Cosmos DB の概要](../cosmos-db/introduction.md) を参照してください。
- [Azure Cosmos DB にデータをインポート](../cosmos-db/import-data.md)する方法を学習してください。

## <a name="functions"></a>Functions

現時点で、Azure Germany からグローバル Azure に Azure Functions リソースを移行することはサポートされていません。 Resource Manager テンプレートをエクスポートし、場所を変更してから、ターゲット リージョンに再デプロイすることをお勧めします。

> [!IMPORTANT]
> 新しいリージョンと一貫性を維持できるように、場所、Azure Key Vault シークレット、証明書、およびその他の GUID を変更します。

詳細:

- [Functions のチュートリアル](https://docs.microsoft.com/azure/azure-functions/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Resource Manager テンプレートをエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)する方法を学習する、または [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の概要を参照してください。
- [Azure Functions の概要](../azure-functions/functions-overview.md)を確認してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
- [テンプレートを再デプロイ](../azure-resource-manager/resource-group-template-deploy.md)する方法を学習してください。

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs の 1 つのインスタンスから別のインスタンスに設定を移行するには、次のように、エクスポートしてからすべての登録トークンおよびタグをインポートします。

1. [既存の通知ハブ登録のエクスポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))を Azure BLOB ストレージ コンテナーに対して行います。
1. ターゲット環境に新しい通知ハブを作成します。
1. BLOB ストレージから新しい通知ハブに、[登録トークンをインポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))します。

詳細:

- [Notification Hubs のチュートリアル](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Notification Hubs の概要](../notification-hubs/notification-hubs-push-notification-overview.md)を確認してください。

## <a name="iot-hub"></a>IoT Hub

Azure Germany からグローバル Azure に Azure IoT Hub インスタンスを移行することはできますが、移行はシームレスではありません。

> [!NOTE]
> この移行が原因で、ご利用の Azure IoT アプリケーション内でダウンタイムとデータ損失が発生する可能性があります。 すべてのテレメトリ メッセージ、C2D コマンド、およびジョブに関連する情報 (スケジュールや履歴) が移行されるわけではありません。 新しい接続文字列の使用を開始するように、ご利用のデバイスとバックエンド アプリケーションを再構成する必要があります。

### <a name="step-1-re-create-the-iot-hub"></a>手順 1:IoT ハブを再作成する

IoT Hub では、ネイティブで複製はサポートされていません。 ただし、[リソース グループをテンプレートとしてエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)する Azure Resource Manager 機能を使用すれば、ご利用の IoT Hub メタデータをエクスポートできます。 構成されたルートやその他の IoT ハブの設定は、エクスポートされたメタデータに含まれています。 次に、グローバル Azure でテンプレートを再デプロイします。 Azure portal では、エクスポートされた JSON の詳細を調べることで、IoT ハブを容易に再作成できる場合があります。

### <a name="step-2-migrate-device-identities"></a>手順 2:デバイス ID を移行する

デバイス ID を移行するには: 

1. Azure Germany のソース テナントでは、[ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) Resource Manager API を使用して、すべてのデバイス ID、デバイス ツイン、およびモジュール ツイン (キーを含む) をストレージ コンテナーにエクスポートすることができます。 Azure Germany でもグローバル Azure でもストレージ コンテナーを使用できます。 生成された共有アクセス署名 URI に十分なアクセス許可が含まれていることを確認します。 
1. [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) Resource Manager API を実行して、ストレージ コンテナーから、グローバル Azure 内の複製された IoT ハブにすべてのデバイス ID をインポートします。
1. 新しい接続文字列の使用を開始するように、ご利用のデバイスとバックエンド サービスを再構成します。 ホスト名が **\*.azure devices.de** から **\*.azure devices.com** に変更されます。  

> [!NOTE]
> Azure Germany とグローバル Azure では、ルート証明機関が異なります。 このことを考慮する必要があるのは、IoT Hub インスタンスとやりとりするデバイスおよびバックエンド アプリケーションを再構成する場合です。

詳細:

- [IoT Hub の ID を一括でエクスポート](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices)する方法を学習してください。
- [IoT Hub の ID を一括でインポート](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices)する方法を学習してください。
- [Azure IoT Hub の概要](../iot-hub/about-iot-hub.md)を確認してください。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
