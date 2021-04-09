---
title: ポータルを使用してマルチテナント アプリへのトラフィックを管理する
titleSuffix: Azure Application Gateway
description: この記事では、既存または新しいアプリケーション ゲートウェイで Azure App Service Web アプリをバックエンド プールのメンバーとして構成する方法について説明します。
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97854912"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway を使用した App Service の構成

App Service は専用のデプロイではなくマルチテナント サービスであるため、受信した要求のホスト ヘッダーを使って、要求に対する適切な App Service エンドポイントが解決されます。 通常、アプリケーションの DNS 名 (App Service に面したアプリケーション ゲートウェイに関連付けられた DNS 名になります) は、バックエンド App Service のドメイン名とは異なります。 したがって、アプリケーション ゲートウェイで受信された元の要求内のホスト ヘッダーは、バックエンド サービスのホスト名と同じではありません。 このため、アプリケーション ゲートウェイからバックエンドへの要求内のホスト ヘッダーが、バックエンド サービスのホスト名に変更されない場合、マルチテナント バックエンドでは、要求を正しいエンドポイントに解決できません。

Application Gateway には、Application Gateway からバックエンドに要求がルーティングされる際に、要求内のホスト ヘッダーをバックエンドのホスト名でオーバーライドする `Pick host name from backend target` というスイッチがあります。 この機能により、Azure App Service や API Management などのマルチテナント バックエンドがサポートされます。 

この記事では、次の方法について説明します。

- バックエンド プールを編集し、そこに App Service を追加する
- "Pick Hostname" スイッチを有効にして HTTP 設定を編集する

## <a name="prerequisites"></a>前提条件

- Application Gateway:バックエンド プール ターゲットなしでアプリケーション ゲートウェイを作成します。 詳細については、「[クイック スタート: Azure Application Gateway による Web トラフィックのルーティング - Azure portal](quick-create-portal.md)

- App Service:既存の App Service がない場合は、[App Service のドキュメント](../app-service/index.yml)を参照してください。

## <a name="add-app-service-as-backend-pool"></a>バックエンド プールとして App Service を追加する

1. Azure portal で、アプリケーション ゲートウェイを選択します。

2. **[バックエンド プール]** で、バックエンド プールを選択します。

4. **[ターゲットの種類]** で、 **[App Services]** を選択します。

5. **[ターゲット]** で、目的の App Service を選択します。

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App Service バックエンド":::
   
   > [!NOTE]
   > このドロップダウンに表示されるのは、お使いの Application Gateway と同じサブスクリプションにある App Service だけです。 Application Gateway とは異なるサブスクリプションの App Service を使用する場合は、 **[ターゲット]** ドロップダウンで **[App Services]** を選択する代わりに、 **[IP アドレスまたはホスト名]** オプションを選択して、App Service のホスト名 (例: azurewebsites.net) を入力してください。
1. **[保存]** を選択します。

## <a name="edit-http-settings-for-app-service"></a>App Service の HTTP 設定を編集する

1. **[HTTP 設定]** で、既存の HTTP 設定を選択します。

2. **[新しいホスト名でオーバーライドする]** で、 **[はい]** を選択します。
3. **[ホスト名をオーバーライドする]** で、 **[バックエンド ターゲットからホスト名を選択する]** を選択します。
4. **[保存]** を選択します。

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="ホスト名をバックエンド HTTP 設定から選択します":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>App Service の相対パスにリダイレクトする場合の追加構成

App Service では、その相対パスにリダイレクトするためのリダイレクト応答をクライアントに送信するとき (`contoso.azurewebsites.net/path1` から `contoso.azurewebsites.net/path2` へのリダイレクトなど)、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (`contoso.com/path2`) を経由するのではなく、`contoso.azurewebsites.net/path2` に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。

実際のユース ケースで、App Service からリダイレクト応答をクライアントに送信すべきシナリオがあれば、[場所ヘッダーを書き換えるための追加手順](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration)を実施してください。

## <a name="restrict-access"></a>アクセスを制限する

これらの例でデプロイした Web アプリでは、インターネットから直接アクセスできるパブリック IP アドレスを使用します。 これは、新機能について学習するときや新しいことを試すときに、トラブルシューティングを行うのに役立ちます。 しかし、機能を運用環境にデプロイする場合は、制限を厳しくする必要があります。

Web アプリへのアクセスを制限できる 1 つの方法は、[Azure App Service 静的 IP 制限](../app-service/app-service-ip-restrictions.md)を使用することです。 たとえば、アプリケーション ゲートウェイからのトラフィックのみを受信するように Web アプリを制限できます。 アプリ サービスの IP 制限の機能を使用して、アクセス権を持つ唯一のアドレスとして、アプリケーション ゲートウェイの VIP を一覧表示します。

## <a name="next-steps"></a>次のステップ

アプリケーション ゲートウェイを使用した App Service とその他のマルチテナント サポートの詳細については、[アプリケーション ゲートウェイを使用したマルチテナント サービスのサポート](./application-gateway-web-app-overview.md)に関するページを参照してください。
