---
title: Azure Application Gateway を使用して App Service Web アプリなどのマルチテナント アプリへのトラフィックを管理する - ポータル
description: この記事では、既存または新しいアプリケーション ゲートウェイで Azure App Service Web アプリをバックエンド プールのメンバーとして構成する方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176210"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway を使用した App Service の構成

Application Gateway では、Azure App Service Web アプリまたはその他のマルチテナント サービスをバックエンド プール メンバーとして使用できます。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> - バックエンド プールを作成し、そこに App Service を追加する
> - "ホスト名の選択" スイッチを有効にして HTTP 設定とカスタム プローブを作成する

## <a name="prerequisites"></a>前提条件

- Application Gateway:既存のアプリケーション ゲートウェイがない場合は、[アプリケーション ゲートウェイの作成方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)に関するページを参照してください。
- App Service:既存の App Service がない場合は、[App Service のドキュメント](https://docs.microsoft.com/azure/app-service/)を参照してください。

## <a name="add-app-service-as-backend-pool"></a>バックエンド プールとして App Service を追加する

1. Azure portal でアプリケーション ゲートウェイの構成ビューを開きます。

2. **[バックエンド プール]** の **[追加]** をクリックして新しいバックエンド プールを作成します。

3. バックエンド プールに適切な名前を付けます。 

4. **[ターゲット]** でドロップダウンをクリックし、オプションとして **[App Service]** を選択します。

5. **[ターゲット]** ドロップダウンのすぐ下にドロップダウンが表示されます。これには App Service の一覧が含まれます。 このドロップダウンから、バックエンド プール メンバーとして追加する App Service を選択し、[追加] をクリックします。

   ![App Service バックエンド](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>App Service の HTTP 設定を作成する

1. **[HTTP 設定]** の **[追加]** をクリックし、新しい HTTP 設定を作成します。

2. HTTP 設定の名前を入力します。必要に応じて Cookie ベースのアフィニティを有効または無効にすることができます。

3. ユース ケースに従って HTTP または HTTPS としてプロトコルを選択します。 

4. **[App Service 用に使用します]** チェックボックスをオンにすると、**[バックエンド アドレスからホスト名を選択するプローブを作成します]** オプションと **[バックエンド アドレスからホスト名を選択します]** オプションが有効になります。 また、このオプションで、スイッチが有効な状態でプローブが自動的に作成され、それがこの HTTP 設定に関連付けられます。

5. **[OK]** をクリックして HTTP 設定を作成します。

   ![HTTP 設定 1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP 設定 2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>リスナー、バックエンド プール、および HTTP 設定を結び付けるルールを作成する

1. **[ルール]** の **[基本]** をクリックして新しい基本ルールを作成します。

2. 適切な名前を入力して、App Service への受信要求を受け入れるリスナーを選択します。

3. **[バックエンド プール]** ドロップダウンで、上で作成したバックエンド プールを選択します。

4. **[HTTP 設定]** ドロップダウンで、上で作成した HTTP 設定を選択します。

5. **[OK]** をクリックしてこのルールを保存します。

   ![ルール](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>アクセスを制限する

これらの例でデプロイした Web アプリでは、インターネットから直接アクセスできるパブリック IP アドレスを使用します。 これは、新機能について学習するときや新しいことを試すときに、トラブルシューティングを行うのに役立ちます。 しかし、機能を運用環境にデプロイする場合は、制限を厳しくする必要があります。

Web アプリへのアクセスを制限できる 1 つの方法は、[Azure App Service 静的 IP 制限](../app-service/app-service-ip-restrictions.md)を使用することです。 たとえば、アプリケーション ゲートウェイからのトラフィックのみを受信するように Web アプリを制限できます。 アプリ サービスの IP 制限の機能を使用して、アクセス権を持つ唯一のアドレスとして、アプリケーション ゲートウェイの VIP を一覧表示します。

## <a name="next-steps"></a>次の手順

アプリケーション ゲートウェイを使用した App Service とその他のマルチテナント サポートの詳細については、[アプリケーション ゲートウェイを使用したマルチテナント サービスのサポート](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)に関するページを参照してください。

App Service からの応答が App Service の URL にリダイレクトされる場合は、[App Service の URL へのリダイレクトに関する問題のトラブルシューティング方法](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)のページを参照してください。
