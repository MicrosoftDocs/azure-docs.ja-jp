---
title: TLS を使用して Web サービスをセキュリティで保護する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でデプロイされた Web サービスをセキュリティで保護するために HTTPS を有効にする方法について説明します。 Azure Machine Learning は、TLS バージョン 1.2 を使用して、Web サービスとしてデプロイされたモデルをセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 3e10841852b8a89b344d3bfd9311db8abe15642a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319543"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning でデプロイされた Web サービスをセキュリティで保護する方法について説明します。

[HTTPS](https://en.wikipedia.org/wiki/HTTPS) を使用して、Web サービスへのアクセスを制限し、クライアントが送信するデータをセキュリティで保護します。 HTTPS は、クライアントと Web サービスの間の通信を暗号化することで双方の間の通信をセキュリティで保護する場合に役立ちます。 暗号化は[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) を使用します。 TLS は、現在も、TLS の前身である *Secure Sockets Layer* (SSL) と呼ばれることがあります。

> [!TIP]
> Azure Machine Learning SDK では、セキュリティで保護された通信に関連するプロパティに "SSL" の用語を使用します。 つまり、Web サービスでは *TLS* を使用しません。 SSL は、より一般的に認識されている用語に過ぎません。
>
> 具体的には、Azure Machine Learning によってデプロイされた Web サービスは、TLS バージョン 1.2 のみをサポートします。

TLS と SSL の両方とも、"*デジタル証明書*" に依存しています。デジタル証明書は、暗号化の実行と ID の検証に役立ちます。 デジタル証明書のしくみの詳細については、ウィキペディアの「[public key infrastructure (公開キー基盤)](https://en.wikipedia.org/wiki/Public_key_infrastructure)」のトピックを参照してください。

> [!WARNING]
> Web サービスで HTTPS を使用しない場合は、そのサービスとの間で送受信されるデータがインターネット上の他のユーザーに表示される場合があります。
>
> HTTPS により、接続先のサーバーの信頼性をクライアントから確認することもできます。 この機能は、[中間者攻撃](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)からクライアントを保護します。

これは、Web サービスをセキュリティで保護する一般的なプロセスです。

1. ドメイン名を取得します。

2. デジタル証明書を取得します。

3. TLS が有効になっている Web サービスをデプロイするか更新します。

4. その Web サービスを指すように DNS を更新します。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) にデプロイする場合は、独自の証明書を購入するか、Microsoft によって提供される証明書を使用することができます。 Microsoft 提供の証明書を使用する場合は、ドメイン名または TLS/SSL 証明書を取得する必要はありません。 詳細については、この記事の「[TLS を有効にしてデプロイする](#enable)」セクションを参照してください。

複数の[デプロイ ターゲット](how-to-deploy-and-where.md)にわたってセキュリティで保護する場合は、わずかな違いがあります。

## <a name="get-a-domain-name"></a>ドメイン名を取得する

ドメイン名をまだ所有していない場合は、*ドメイン名レジストラー* から購入します。 プロセスと価格は、レジストラーによって異なります。 レジストラーは、ドメイン名を管理するためのツールを提供します。 これらのツールを使用して、完全修飾ドメイン名 (FQDN) (www\.contoso.com など) を、Web サービスをホストする IP アドレスにマップします。

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL 証明書を取得する

TLS/SSL 証明書 (デジタル証明書) を取得する方法はたくさんあります。 最も一般的な方法は、*証明機関* (CA) から購入することです。 証明書の取得場所に関係なく、次のファイルが必要です。

* **証明書**。 証明書は、完全な証明書チェーンを含み、"PEM でエンコード" されている必要があります。
* **キー**。 キーも、PEM でエンコードされている必要があります。

証明書を要求するときは、Web サービスで使用する予定のアドレスの FQDN を指定する必要があります (www\.contoso.com など)。 Web サービスの ID を検証するときに、証明書に記載されているアドレスとクライアントによって使用されるアドレスが比較されます。 これらのアドレスが一致しない場合、クライアントにはエラー メッセージが返されます。

> [!TIP]
> 証明機関が、証明書とキーを PEM でエンコードされたファイルとして提供できない場合は、 [OpenSSL](https://www.openssl.org/) などのユーティリティを使用して、フォーマットを変更できます。

> [!WARNING]
> *自己署名*証明書は開発時にのみ使用します。 運用環境では使用しないでください。 自己署名証明書により、お使いのクライアント アプリケーションで問題が発生する可能性があります。 詳しくは、お使いのクライアント アプリケーションで使用されているネットワーク ライブラリに関するドキュメントをご覧ください。

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> TLS を有効にしてデプロイする

TLS が有効なサービスをデプロイ (または再デプロイ) するには、"*ssl_enabled*" パラメーターを "True" に設定します (該当する場合は必ず)。 *ssl_certificate* パラメーターを *certificate* ファイルの値に設定します。 *ssl_key* を *key* ファイルの値に設定します。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>AKS およびフィールド プログラマブル ゲート アレイ (FPGA) にデプロイする

  > [!NOTE]
  > このセクションの情報は、デザイナー用のセキュリティで保護された Web サービスをデプロイするときにも適用されます。 Python SDK に慣れていない場合は、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) に関するページを参照してください。

AKS にデプロイする場合、新しい AKS クラスターを作成するか、既存のクラスターを接続することができます。 クラスターの作成または接続について詳しくは、「[Azure Kubernetes Service クラスターにモデルをデプロイする](how-to-deploy-azure-kubernetes-service.md)」をご覧ください。
  
-  新しいクラスターを作成する場合、 **[AksCompute.provisionining_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** を使用します。
- 既存のクラスターを接続する場合、 **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** を使用します。 どちらも **enable_ssl** メソッドを持つ構成オブジェクトが返されます。

**enable_ssl** メソッドは、Microsoft によって提供される証明書でも、購入する証明書を使用することもできます。

  * Microsoft 提供の証明書を使用する場合、*leaf_domain_label* パラメーターを使用する必要があります。 このパラメーターは、サービスの DNS 名を生成します。 たとえば、"contoso" という値を使用すると "contoso\<six-random-characters>.\<azureregion>.cloudapp.azure.com" というドメイン名が作成されます。この場合の \<azureregion> は、サービスが含まれるリージョンを意味しています。 必要に応じて、*overwrite_existing_domain* パラメーターを使用して既存の *leaf_domain_label* を上書きできます。

    TLS が有効なサービスをデプロイ (または再デプロイ) するには、"*ssl_enabled*" パラメーターを "True" に設定します (該当する場合は必ず)。 *ssl_certificate* パラメーターを *certificate* ファイルの値に設定します。 *ssl_key* を *key* ファイルの値に設定します。

    > [!IMPORTANT]
    > Microsoft 提供の証明書を使用する場合、独自の証明書またはドメイン名を購入する必要はありません。

    次の例では、Microsoft 提供の TLS/SSL 証明書を有効にする構成の作成方法を示します。

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * *購入した証明書*を使用する場合、*ssl_cert_pem_file*、*ssl_key_pem_file*、および *ssl_cname* のパラメーターを使用します。 次の例では、 *.pem* ファイルを使用して、購入した TLS/SSL 証明書を使用する構成を作成する方法を示します。

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

*enable_ssl* の詳細については、[AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) および [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) に関する記事を参照してください。

### <a name="deploy-on-azure-container-instances"></a>Azure Container Instances にデプロイする

Azure Container Instances にデプロイするとき、次のコード スニペットに示すように、TLS 関連のパラメーターの値を入力します。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

詳細については、[AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) に関する記事を参照してください。

## <a name="update-your-dns"></a>DNS を更新する

次に、Web サービスを指すように DNS を更新する必要があります。

+ **Container Instances の場合:**

  ドメイン名レジストラー提供のツールを使用して、ドメイン名の DNS レコードを更新します。 レコードは、サービスの IP アドレスを指している必要があります。

  レジストラーと、ドメイン名に対して構成された "Time to Live" (TTL) に応じて、クライアントがドメイン名を解決できるまで、数分から数時間の遅延が生じることがあります。

+ **AKS の場合:**

  > [!WARNING]
  > Microsoft 提供の証明書を使用してサービスを作成するために *leaf_domain_label* を使用した場合は、クラスターの DNS 値を手動で更新しないでください。 この値は、自動的に設定される必要があります。

  左ウィンドウの **[設定]** タブの下の **[構成]** タブで、AKS クラスターのパブリック IP アドレスの DNS を更新します。 (次の図を参照してください)。パブリック IP アドレスは、AKS エージェント ノードとその他のネットワーク リソースを含むリソース グループの下に作成されるリソースの種類です。

  [![Azure Machine Learning: TLS を使用して Web サービスをセキュリティで保護する](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>TLS/SSL 証明書を更新する

TLS/SSL 証明書には有効期限切れがあるため、更新する必要があります。 通常、これは毎年発生します。 Azure Kubernetes Service にデプロイされているモデルの証明書を更新するには、次のセクションの情報を参照してください。

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft が生成した証明書を更新する

証明書が元は Microsoft によって (*leaf_domain_label* を使用してサービスを作成するときに) 生成されたものである場合、次のいずれかの例を使用して証明書を更新します。

> [!IMPORTANT]
> * 既存の証明書がまだ有効な場合は、`renew=True` (SDK) か `--ssl-renew` (CLI) を使用して、構成によってこれを強制的に更新します。 たとえば、既存の証明書があと 10 日間有効で、`renew=True` を使用しない場合、証明書は更新されない可能性があります。
> * サービスが最初にデプロイされたとき、`leaf_domain_label` が使用されて、`<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` というパターンで DNS 名が作成されました。 既存の名前 (最初に生成された 6 桁を含む) を維持するには、元の `leaf_domain_label` 値を使用します。 生成された 6 桁の数字は含めないでください。

**SDK の使用**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**CLI の使用**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

詳細については、次のドキュメントを参照してください。

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>カスタム証明書を更新する

証明書が元は証明機関によって生成されたものである場合は、次の手順を使用します。

1. 証明機関によって提供されるドキュメントを使用して、証明書を更新します。 このプロセスによって新しい証明書ファイルが作成されます。

1. SDK または CLI のいずれかを使用して、新しい証明書でサービスを更新します。

    **SDK の使用**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **CLI の使用**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

詳細については、次のドキュメントを参照してください。

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>TLS を無効にする

Azure Kubernetes Service にデプロイされたモデルに対して TLS を無効にするには、`status="Disabled"` を指定して `SslConfiguration` を作成してから、更新を実行します。

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>次のステップ
具体的には、次の方法を学習します。
+ [Web サービスとしてデプロイされた機械学習モデルを使用する](how-to-consume-web-service.md)
+ [Azure Virtual Network 内で実験と推論を安全に実行する](how-to-enable-virtual-network.md)
