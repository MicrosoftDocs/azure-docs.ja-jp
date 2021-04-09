---
title: Azure API Management に Service Fabric バックエンドを設定する | Microsoft Docs
description: Azure portal を使用して Azure API Management で Service Fabric サービス バックエンドを作成する方法
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99499431"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Azure portal を使用して API Management に Service Fabric バックエンドを設定する

この記事では、Azure portal を使用して、[Service Fabric](../service-fabric/service-fabric-api-management-overview.md) サービスをカスタム API バックエンドとして構成する方法を示します。 デモンストレーションを目的として、ASP.NET Core の基本的なステートレス リライアブル サービスを Service Fabric バックエンドとして設定する方法を示します。

背景については、「[API Management のバックエンド](backends.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

カスタム バックエンドとして Windows を実行している Service Fabric クラスターでサンプル サービスを構成するための前提条件:

* **Windows 開発環境** - [Visual Studio 2019](https://www.visualstudio.com) と **Azure 開発**、**ASP.NET と Web 開発**、および **.NET Core クロスプラットフォーム開発** のワークロードをインストールします。 その後、[.NET 開発環境](../service-fabric/service-fabric-get-started.md)をセットアップします。

* **Service Fabric クラスター** - 「[チュートリアル: Azure 仮想ネットワークに Windows を実行する Service Fabric クラスターをデプロイする](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md)」を参照してください。 既存の X.509 証明書を使用してクラスターを作成することも、テスト目的で新しい自己署名証明書を作成することもできます。 クラスターは仮想ネットワーク内に作成されます。

* **Service Fabric のサンプル アプリ**- 「[Azure で API Management と Service Fabric を統合する](../service-fabric/service-fabric-tutorial-deploy-api-management.md)」の説明に従って、Web API アプリを作成し、Service Fabric クラスターにデプロイします。

    これらの手順では、既定の Web API プロジェクト テンプレートを使用して、ASP.NET Core の基本的なステートレス リライアブル サービスを作成します。 後で、Azure API Management を通じて、このサービスの HTTP エンドポイントを公開します。

    アプリケーション名 (`fabric:/myApplication/myService` など) をメモしておいてください。 

* **API Management インスタンス** - **Premium** または **Developer** レベルであり、Service Fabric クラスターと同じリージョン内の、既存あるいは新規の API Management インスタンス。 必要に応じて、[API Management インスタンスを作成します](get-started-create-service-instance.md)。

* **仮想ネットワーク** - Service Fabric クラスター用に作成した仮想ネットワークに API Management インスタンスを追加します。 API Management では、仮想ネットワーク内に専用サブネットが必要です。

  API Management インスタンスの仮想ネットワーク接続を有効にする手順については、[Azure API Management で仮想ネットワークを使用する方法](api-management-using-with-vnet.md)に関するページを参照してください。

## <a name="create-backend---portal"></a>バックエンドを作成する - ポータル

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>API Management に Service Fabric クラスター証明書を追加する

Service Fabric クラスター証明書は、クラスターに関連付けられている Azure キー コンテナーで格納および管理されます。 この証明書をクライアント証明書として API Management インスタンスに追加します。

API Management インスタンスに証明書を追加する手順については、「[Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法](api-management-howto-mutual-certificates.md)」を参照してください。 

> [!NOTE]   
> キー コンテナー証明書を参照し、API Management に証明書を追加することをお勧めします。 

### <a name="add-service-fabric-backend"></a>Service Fabric バックエンドを追加する

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[API]** で、 **[バックエンド]**  >  **[+ 追加]** の順に選択します。
1. バックエンド名と省略可能な説明を入力します
1. **[種類]** で、 **[Service Fabric]** を選択します。
1. **[ランタイム URL]** に、API Management からの要求の転送先となる Service Fabric バックエンド サービスの名前を入力します。 例: `fabric:/myApplication/myService`. 
1. **[Maximum number of partition resolution retries]\(パーティションの解決の最大再試行回数\)** に、0 から 10 までの数値を入力します。
1. Service Fabric クラスターの管理エンドポイントを入力します。 このエンドポイントは、ポート `19080` 上のクラスターの URL (`https://mysfcluster.eastus.cloudapp.azure.com:19080` など) です。
1. **[クライアント証明書]** で、前のセクションで API Management インスタンスに追加した Service Fabric クラスター証明書を選択します。
1. **[Management endpoint authorization method]\(管理エンドポイントの承認方法\)** で、TLS 通信に Service Fabric クラスター管理サービスによって使用される証明書の拇印またはサーバー X509 名を入力します。
1. **[Validate certificate chain]\(証明書チェーンを検証する\)** および **[Validate certificate name]\(証明書名を検証する\)** の設定を有効にします。
1. **[Authorization credentials]\(承認資格情報\)** では、Service Fabric で構成されたバックエンド サービスに接続するための資格情報を必要に応じて指定します。 このシナリオで使用するサンプル アプリでは、承認資格情報は必要ありません。
1. **［作成］** を選択します

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service Fabric バックエンドを作成する":::

## <a name="use-the-backend"></a>バックエンドを使用する

カスタム バックエンドを使用するには、[`set-backend-service`](api-management-transformation-policies.md#SetBackendService) ポリシーを使ってそれを参照します。 このポリシーを使うと、受信 API 要求の既定のバックエンド サービス ベース URL が、指定されたバックエンド (この場合は Service Fabric バックエンド) に変換されます。 

`set-backend-service` ポリシーは、既存の API を使用して、API 設定で指定されているものとは異なるバックエンドに受信要求を変換するのに役立つ場合があります。 この記事ではデモンストレーションを目的として、テスト API を作成し、API 要求を Service Fabric バックエンドに送信するようにポリシーを設定します。 

### <a name="create-api"></a>API の作成

「[手動による API の追加](add-api-manually.md)」の手順に従って、空の API を作成します。

* API 設定では、 **[Web サービスの URL]** を空白のままにします。
* *fabric* などの **[API URL サフィックス]** を追加します。

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="空の API を作成":::

### <a name="add-get-operation-to-the-api"></a>API に GET 操作を追加する

「[Service Fabric のバックエンド サービスのデプロイ](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)」に示されているように、Service Fabric クラスターにデプロイされたサンプルの ASP.NET Core サービスでは、URL パス `/api/values` での 1 つの HTTP GET 操作がサポートされています。

そのパスでの既定の応答は、次の 2 つの文字列の JSON 配列です。

```json
["value1", "value2"]
```

クラスターとの API Management の統合をテストするには、対応する GET 操作をパス `/api/values` の API に追加します。

1. 前の手順で作成した API を選びます。
1. **[+ Add Operation] \(+ 操作の追加\)** を選択します。
1. **[フロントエンド]** ウィンドウで、次の値を入力して、 **[保存]** を選択します。

     | 設定             | 値                             | 
    |---------------------|-----------------------------------|
    | **表示名**    | *[Test backend]\(バックエンドのテスト\)*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="API に GET 操作を追加する":::

### <a name="configure-set-backend-policy"></a>`set-backend` ポリシーを構成する

[`set-backend-service`](api-management-transformation-policies.md#SetBackendService) ポリシーをテスト API に追加します。

1. **[デザイン]** タブの **[受信処理]** セクションで、コード エディター ( **</>** ) アイコンを選択します。 
1. **&lt;inbound&gt;** 要素内にカーソルを置きます
1. 次のポリシー ステートメントを追加します。 `backend-id` で、ご自分の Service Fabric バックエンドの名前に置き換えます。

   `sf-resolve-condition` は、クラスター パーティションが解決されない場合の再試行条件です。 再試行の回数はバックエンドの構成時に設定されたものです。

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. **[保存]** を選択します。

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="set-backend-service ポリシーを構成する":::

### <a name="test-backend-api"></a>バックエンド API をテストする

1. **[テスト]** タブで、前のセクションで作成した **GET** 操作を選択します。
1. **[送信]** を選択します。

適切に構成されている場合は、HTTP 応答で HTTP 成功コードが示され、バックエンドの Service Fabric サービスから返された JSON が表示されます。

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Service Fabric バックエンドをテストする":::

## <a name="next-steps"></a>次のステップ

* バックエンドに要求を転送するように[ポリシーを構成する](api-management-advanced-policies.md)方法について学習する
* バックエンドは、API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)、または [Azure Resource Manager テンプレート](../service-fabric/service-fabric-tutorial-deploy-api-management.md)を使用して構成することもできます

