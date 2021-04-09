---
title: Azure App Service リソースの移動
description: Azure Resource Manager を使用して、App Service リソースを新しいリソース グループまたはサブスクリプションに移動します。
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531374"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service リソースの移動に関するガイダンス

この記事では、App Service リソースを移動する手順について説明します。 App Service リソースを新しいサブスクリプションに移動するためには、特定の要件があります。

## <a name="move-across-subscriptions"></a>サブスクリプション間で移動する

Web アプリをサブスクリプション間で移動する場合には、次のガイダンスが適用されます。

- 移動先のリソース グループに既存の App Service リソースが含まれていてはいけません。 App Service リソースには次のものがあります。
    - Web Apps
    - App Service プラン
    - アップロードまたはインポートした TLS/SSL 証明書
    - App Service Environment
- リソース グループ内のすべての App Service リソースを一緒に移動する必要があります。
- App Service Environment を新しいリソース グループまたはサブスクリプションに移動することはできません。 ただし、App Service Environment を移動せずに、Web アプリと App Service プランを新しいサブスクリプションに移動することはできます。 移動後、Web アプリは App Service Environment でホストされなくなります。
- リソース グループ内の他のすべてのリソースと共に証明書が移動される限りは、TLS バインドを削除せずに Web にバインドされた証明書を移動することができます。
- App Service リソースは、最初に作成されたときのリソース グループからのみ移動できます。 元のリソース グループ内には App Service リソースが既にない場合は、元のリソース グループに戻します。 次に、サブスクリプション間でリソースを移動します。

元のリソース グループを覚えていない場合は、診断を使用して見つけることができます。 Web アプリの場合、 **[問題の診断と解決]** を選択します。 その後、 **[構成と管理]** を選択します。

![診断の選択](./media/app-service-move-limitations/select-diagnostics.png)

**[移行オプション]** を選択します。

![移行オプションの選択](./media/app-service-move-limitations/select-migration.png)

Web アプリの移動に推奨される手順のオプションを選択します。

![推奨される手順の選択](./media/app-service-move-limitations/recommended-steps.png)

リソースを移動する前に推奨されるアクションをご確認ください。 これには、Web アプリの元のリソース グループに関する情報が含まれています。

![スクリーン キャプチャには、Microsoft ドット Web リソースを移動するための推奨される手順が示されています。](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移動のサポート

どの App Service リソースを移動できるかを判断するには、以下に関して、移動のサポートの状態を確認します。

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>次のステップ

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../move-resource-group-and-subscription.md)」を参照してください。
