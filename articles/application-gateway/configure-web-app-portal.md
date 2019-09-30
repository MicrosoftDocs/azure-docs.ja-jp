---
title: Azure Application Gateway を使用して App Service Web アプリなどのマルチテナント アプリへのトラフィックを管理する - ポータル
description: この記事では、既存または新しいアプリケーション ゲートウェイで Azure App Service Web アプリをバックエンド プールのメンバーとして構成する方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: dee4859c57172a703517848510a31b70ff1f24cd
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "68370414"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway を使用した App Service の構成

App Service は専用のデプロイではなくマルチテナント サービスであるため、受信要求のホスト ヘッダーを使って、要求に対する適切な App Service エンドポイントが解決されます。 通常、アプリケーションの DNS 名 (App Service に面したアプリケーション ゲートウェイに関連付けられた DNS 名になります) は、バックエンド App Service のドメイン名とは異なります。 したがって、アプリケーション ゲートウェイで受信された元の要求内のホスト ヘッダーは、バックエンド サービスのホスト名と同じではありません。 このため、アプリケーション ゲートウェイからバックエンドへの要求内のホスト ヘッダーが、バックエンド サービスのホスト名に変更されない場合、マルチテナント バックエンドでは、要求を正しいエンドポイントに解決できません。

Application Gateway には、Application Gateway からバックエンドに要求がルーティングされる際に、要求内のホスト ヘッダーをバックエンドのホスト名でオーバーライドする `Pick host name from backend address` というスイッチがあります。 この機能により、Azure App Service や API Management などのマルチテナント バックエンドがサポートされます。 

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
   
   > [!NOTE]
   > このドロップダウンに表示されるのは、Application Gateway と同じサブスクリプションに属している App Service だけです。 Application Gateway とは異なるサブスクリプションの App Service を使用する場合は、 **[ターゲット]** ドロップダウンで **[App Services]** を選択する代わりに、 **[IP アドレスまたはホスト名]** オプションを選択して、App Service のホスト名 (例: azurewebsites.net) を入力してください。

## <a name="create-http-settings-for-app-service"></a>App Service の HTTP 設定を作成する

1. **[HTTP 設定]** の **[追加]** をクリックし、新しい HTTP 設定を作成します。

2. HTTP 設定の名前を入力します。必要に応じて Cookie ベースのアフィニティを有効または無効にすることができます。

3. ユース ケースに従って HTTP または HTTPS としてプロトコルを選択します。 

   > [!NOTE]
   > HTTPS を選択した場合、App Service バックエンドをホワイトリストに登録するために、認証証明書や信頼されたルート証明書をアップロードする必要はありません。App Service は信頼された Azure サービスであるためです。

4. **[App Service 用に使用します]** のチェック ボックスをオンにします。 `Create a probe with pick host name from backend address` と `Pick host name from backend address` のスイッチは自動的に有効になることに注意してください。`Pick host name from backend address` は、Application Gateway からバックエンドに要求がルーティングされる際に、要求内のホスト ヘッダーをバックエンドのホスト名でオーバーライドします。  

   `Create a probe with pick host name from backend address` では、正常性プローブが自動的に作成されて、この HTTP 設定に関連付けられます。 その他の正常性プローブをこの HTTP 設定に作成する必要はありません。 <HTTP Setting name><Unique GUID> という名前の新しいプローブが正常性プローブの一覧に追加されていること、また既にスイッチ `Pick host name from backend http settings enabled` が設定されていることを確認できます。

   App Service に使用されている HTTP 設定が既に 1 つ以上あって、自分が作成しているプローブで使っているものと同じプロトコルがそれらの HTTP 設定に使用されている場合は、`Create a probe with pick host name from backend address` スイッチの代わりに、いずれかのカスタム プローブを選択するためのドロップダウンが表示されます。 これは、App Service を含む HTTP 設定が既に存在しているために、`Pick host name from backend http settings enabled` スイッチが設定された正常性プローブも存在しているからです。 ドロップダウンからカスタム プローブを選択してください。

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

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>App Service の相対パスにリダイレクトする場合の追加構成

App Service では、その相対パスにリダイレクトするためのリダイレクト応答をクライアントに送信するとき (contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトなど)、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (contoso.com/path2) を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。

実際のユース ケースで、App Service からリダイレクト応答をクライアントに送信すべきシナリオがあれば、[場所ヘッダーを書き換えるための追加手順](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)を実施してください。

## <a name="restrict-access"></a>アクセスを制限する

これらの例でデプロイした Web アプリでは、インターネットから直接アクセスできるパブリック IP アドレスを使用します。 これは、新機能について学習するときや新しいことを試すときに、トラブルシューティングを行うのに役立ちます。 しかし、機能を運用環境にデプロイする場合は、制限を厳しくする必要があります。

Web アプリへのアクセスを制限できる 1 つの方法は、[Azure App Service 静的 IP 制限](../app-service/app-service-ip-restrictions.md)を使用することです。 たとえば、アプリケーション ゲートウェイからのトラフィックのみを受信するように Web アプリを制限できます。 アプリ サービスの IP 制限の機能を使用して、アクセス権を持つ唯一のアドレスとして、アプリケーション ゲートウェイの VIP を一覧表示します。

## <a name="next-steps"></a>次の手順

アプリケーション ゲートウェイを使用した App Service とその他のマルチテナント サポートの詳細については、[アプリケーション ゲートウェイを使用したマルチテナント サービスのサポート](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)に関するページを参照してください。
