---
title: チュートリアル - 既存のカスタム ドメインを Azure Spring Cloud にマップする
description: 既存のカスタム Distributed Name Service (DNS) 名を Azure Spring Cloud にマップする方法
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 05107d0246be2273c09e91573bd30a4108ac7795
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290329"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>既存のカスタム ドメインを Azure Spring Cloud にマップする
ドメイン ネーム サービス (DNS) は、ネットワーク全体のネットワーク ノード名を格納するための手法です。 このチュートリアルでは、CNAME レコードを使用して、 www.contoso.com などのドメインをマップします。 証明書を使用してカスタム ドメインをセキュリティで保護し、トランスポート層セキュリティ (TLS。Secure Sockets Layer (SSL) とも呼ばれます) を強制する方法を示します。 

証明書は、Web トラフィックを暗号化します。 これらの TLS/SSL 証明書は、Azure Key Vault に格納できます。 

## <a name="prerequisites"></a>前提条件
* Azure Spring Cloud にデプロイされたアプリケーション (「[クイックスタート: Azure portal を使用して既存の Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart.md)」を参照するか、既存のアプリを使用してください)。
* GoDaddy などのドメイン プロバイダーの DNS レジストリへのアクセス権を持つドメイン名。
* サードパーティ プロバイダーからのプライベート証明書 (つまり自己署名証明書)。 証明書はドメインと一致している必要があります。
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) のデプロイ済みインスタンス

## <a name="import-certificate"></a>証明書のインポート 
証明書をインポートする手順では、PEM または PFX でエンコードされたファイルがディスク上にあり、秘密キーを持っている必要があります。 

証明書をキー コンテナーにアップロードするには、次の操作を行います。
1. キー コンテナー インスタンスに移動します。
1. 左側のナビゲーション ペインで、 **[証明書]** をクリックします。
1. 上部のメニューで、 **[生成/インポート]** をクリックします。
1. **[証明書の作成]** ダイアログの **[証明書の作成方法]** で、[`Import`] を選択します。
1. **[証明書ファイルのアップロード]** で、証明書の場所に移動し、選択します。
1. **[パスワード]** で、証明書の秘密キーを入力します。
1. **Create** をクリックしてください。

    ![証明書のインポート 1](./media/custom-dns-tutorial/import-certificate-a.png)

証明書をインポートする前に、次のようにして Azure Spring Cloud にキー コンテナーへのアクセス権を付与します。
1. キー コンテナー インスタンスに移動します。
1. 左側のナビゲーション ペインで、 **[アクセス ポリシー]** をクリックします。
1. 上部のメニューで、 **[アクセス ポリシーの追加]** をクリックします。
1. 情報を入力し、 **[追加]** 、 **[保存]** の順にクリックしてアクセス ポリシーを保存します。

| Secret permission (シークレットのアクセス許可) | 証明書の権限 | プリンシパルの選択 |
|--|--|--|
| Get、List | Get、List | Azure Spring cloud Domain-Management (Azure Spring Cloud ドメイン - 管理) |

![証明書のインポート 2](./media/custom-dns-tutorial/import-certificate-b.png)

または、Azure CLI を使用して、Azure Spring Cloud にキー コンテナーへのアクセス権を付与することもできます。

次のコマンドを使用して、オブジェクト ID を取得します。
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Azure Spring Cloud にキー コンテナーへの読み取りアクセス権を付与します。次のコマンドではオブジェクト ID を置き換えてください。
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Azure Spring Cloud に証明書をインポートするには、次の操作を行います。
1. サービス インスタンスに移動します。 
1. アプリの左側のナビゲーション ペインで、 **[TLS/SSL の設定]** を選択します。
1. その後、 **[Key Vault 証明書のインポート]** をクリックします。

    ![証明書のインポート](./media/custom-dns-tutorial/import-certificate.png)

または、Azure CLI を次のように使用して、証明書をインポートすることもできます。

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> 前の証明書のインポート コマンドを実行する前に、必ず Azure Spring Cloud にキー コンテナーへのアクセス権を付与してください。 まだの場合は、次のコマンドを実行してアクセス権を付与できます。

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

証明書が正常にインポートされると、 **[秘密キー証明書]** の一覧に表示されます。

![秘密キー証明書](./media/custom-dns-tutorial/key-certificates.png)

または、Azure CLI を次のように使用して、証明書の一覧を表示することもできます。

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書を特定のドメインにバインドする必要があります。 このドキュメントの「**SSL バインドの追加**」の手順に従ってください。

## <a name="add-custom-domain"></a>カスタム ドメインの追加
カスタム DNS 名を Azure Spring Cloud にマップするには、CNAME レコードを使用できます。 

> [!NOTE] 
> A レコードはサポートされていません。 

### <a name="create-the-cname-record"></a>CNAME レコードを作成する
DNS プロバイダーに移動し、CNAME レコードを追加して、ドメインを <service_name>.azuremicroservices.io にマップします。 ここで、<service_name> は Azure Spring Cloud インスタンスの名前です。 ワイルドカード ドメインとサブ ドメインがサポートされています。 CNAME を追加した後の DNS レコード ページは、次の例のようになります。 

![DNS レコード ページ](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Azure Spring Cloud アプリへのカスタム ドメインのマップ
Azure Spring Cloud にアプリケーションがない場合は、「[クイックスタート: Azure portal を使用して既存の Azure Spring Cloud アプリケーションを起動する](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master)」の手順に従ってください。

アプリケーション ページに移動します。

1. **[カスタム ドメイン]** を選択します。
2. 次に、 **[カスタム ドメインの追加]** を選択します。 

    ![カスタム ドメイン](./media/custom-dns-tutorial/custom-domain.png)

3. CNAME レコードを追加した完全修飾ドメイン名 (www.contoso.com など) を入力します。 ホスト名レコード タイプが CNAME に設定されていることを確認します (<service_name>.azuremicroservices.io)
4. **[検証]** をクリックすると、 **[追加]** ボタンが有効になります。
5. **[追加]** をクリックします。

    ![カスタム ドメインの追加](./media/custom-dns-tutorial/add-custom-domain.png)

または、Azure CLI を次のように使用して、カスタム ドメインを追加することもできます。
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

1 つのアプリは複数のドメインを持つことができますが、1 つのドメインは 1 つのアプリにのみマップできます。 カスタムド メインがアプリに正常にマップされると、カスタム ドメイン テーブルにそのドメインが表示されます。

![カスタム ドメイン テーブル](./media/custom-dns-tutorial/custom-domain-table.png)

または、Azure CLI を次のように使用して、カスタム ドメインの一覧を表示することもできます。
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> カスタム ドメインの **[セキュリティ保護なし]** というラベルは、まだ SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、エラーまたは警告が表示されます。

## <a name="add-ssl-binding"></a>SSL バインドの追加
前の図に示されているように、カスタム ドメイン テーブルで **[SSL バインディングの追加]** を選択します。  
1. **[証明書]** で選択するか、証明書をインポートします。
1. **[保存]** をクリックします。

    ![SSL バインディングの追加 1](./media/custom-dns-tutorial/add-ssl-binding.png)

または、Azure CLI を次のように使用して、**SSL バインディングを追加する**こともできます。
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

SSL バインディングが正常に追加されると、ドメインの状態は安全になり、 **[正常]** と表示されます。 

![SSL バインディングの追加 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS の適用
既定では、ユーザーは引き続き HTTP を使用してアプリにアクセスできますが、管理者はすべての HTTP 要求を HTTPS ポートにリダイレクトすることができます。

アプリ ページで、左側のナビゲーションにある **[カスタム ドメイン]** を選択します。 次に、 **[HTTPS のみ]** を *True* に設定します。

![SSL バインディングの追加 3](./media/custom-dns-tutorial/enforce-http.png)

または、Azure CLI を次のように使用して、HTTPS を適用することもできます。
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

操作が完了したら、アプリを指す HTTPS URL のいずれかに移動します。 HTTP URL では機能しないことに注意してください。

## <a name="see-also"></a>関連項目
* [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [証明書のインポート](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Azure CLI を使用して Spring Cloud アプリを起動する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

