---
title: Azure App Service リソースを新しいサブスクリプションまたはリソース グループへ移動する
description: Azure Resource Manager を使用して、App Service リソースを新しいリソース グループまたはサブスクリプションに移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723375"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service リソースの移動に関するガイダンス

App Service リソースを移動する手順は、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。

## <a name="move-in-same-subscription"></a>同じサブスクリプション内で移動する

Web アプリを "_同じサブスクリプション内_" で移動する場合には、サードパーティの SSL 証明書は移動できません。 ただし、サードパーティの証明書を移動せずに Web アプリを新しいリソース グループに移動することはできます。その場合でも、お客様のアプリの SSL 機能は引き続き機能します。

SSL 証明書を Web アプリと共に移動したい場合は、次の手順に従います。

1. サードパーティの証明書を Web アプリから削除します。ただし、お客様の証明書のコピーは維持してください。
2. Web アプリを移動します。
3. 移動した Web アプリにサードパーティの証明書をアップロードします。

## <a name="move-across-subscriptions"></a>サブスクリプション間で移動する

Web App を _サブスクリプション間_ で移動する場合には、次の制限事項が適用されます。

- 移動先のリソース グループに既存の App Service リソースが含まれていてはいけません。 App Service リソースには次のものがあります。
    - Web Apps
    - App Service プラン
    - アップロードまたはインポートした SSL 証明書
    - App Service Environment
- リソース グループ内のすべての App Service リソースを一緒に移動する必要があります。
- App Service リソースは、最初に作成されたときのリソース グループからのみ移動できます。 元のリソース グループ内には App Service リソースが既にない場合は、元のリソース グループに戻します。 次に、サブスクリプション間でリソースを移動します。

元のリソース グループを覚えていない場合は、診断を使用して見つけることができます。 Web アプリの場合、 **[問題の診断と解決]** を選択します。 その後、 **[構成と管理]** を選択します。

![診断の選択](./media/app-service-move-limitations/select-diagnostics.png)

**[移行オプション]** を選択します。

![移行オプションの選択](./media/app-service-move-limitations/select-migration.png)

Web アプリの移動に推奨される手順のオプションを選択します。

![推奨される手順の選択](./media/app-service-move-limitations/recommended-steps.png)

リソースを移動する前に推奨されるアクションをご確認ください。 これには、Web アプリの元のリソース グループに関する情報が含まれています。

![Recommendations](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>App Service 証明書を移動する

お客様の App Service 証明書は、新しいリソース グループまたはサブスクリプションに移動できます。 お客様の App Service 証明書が Web アプリにバインドされている場合は、新しいサブスクリプションにリソースを移動する前に、いくつかの手順に従う必要があります。 リソースを移動する前に、SSL バインディングとプライベート証明書を Web アプリから削除してください。 App Service 証明書を削除する必要はありません。Web アプリのプライベート証明書だけ削除してください。

## <a name="move-support"></a>移動のサポート

どの App Service リソースを移動できるかを判断するには、以下に関して、移動のサポートの状態を確認します。

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>次の手順

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。
