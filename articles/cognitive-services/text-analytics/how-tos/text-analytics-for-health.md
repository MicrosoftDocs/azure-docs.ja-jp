---
title: Text Analytics for Health を使用する方法
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health を使用して、構造化されていない臨床テキストから医療情報を抽出してラベル付けする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 5bb244796414c828e1535e4874fc85aa83f182dc
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300070"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>方法:Text Analytics for Health (プレビュー) を使用する

> [!NOTE]
> Text Analytics for Health コンテナーは最近更新されました。 最近の変更の詳細については、[新機能](../whats-new.md)に関する記事をご覧ください。 一覧にある新機能を使用するには、必ず最新のコンテナーをプルしてください。

> [!IMPORTANT] 
> Text Analytics for Health は、"現状のまま"、"保証なしで" 提供されるプレビュー機能です。 そのため、**Text Analytics for Health (プレビュー) は、運用環境に実装またはデプロイして使用しないでください。** Text Analytics for Health は、医療デバイス、臨床サポート、診断ツール、または他のテクノロジ (病気や他の状況の診断、治療、軽減、取り扱い、防止での使用が意図されているもの) として使用することを意図されたり、使用できるようにされているものではなく、そのような目的でのこの機能の使用に対して、マイクロソフトからはライセンスや権利は付与されません。 この機能は、専門的な医療のアドバイスや医学的意見、診断、治療、または医療専門家による医学的判断に代わるものとして実装またはデプロイするために設計されたり、それを意図されたりしたものではなく、そのようには使用しないでください。 お客様は、Text Analytics for Health の使用に関するすべての責任を持ちます。 Microsoft では、Text Analytics for Health またはその機能に関連して提供されるすべての資料が、すべての医療目的に対して十分であること、またはすべての人の健康的または医療的な要件を満たすことについて、いっさい保証しません。 


Text Analytics for Health は、医師のメモ、退院要約、臨床ドキュメント、電子健康記録などの非構造化テキストから関連する医療情報を抽出してラベル付けする、コンテナー化されたサービスです。  

## <a name="features"></a>特徴

Text Analytics for Health コンテナーでは、現在、特定のセキュリティとデータ ガバナンスの要件を満たす、独自の開発環境において、英語のテキストに対する固有表現認識 (NER)、関係抽出、エンティティ否定、エンティティ リンク設定が実行されます。

#### <a name="named-entity-recognition"></a>[名前付きエンティティの認識](#tab/ner)

固有表現認識では、非構造化テキストに記載されている語句が検出され、それを診断、薬剤名、症状や兆候、年齢など、1 つまたは複数のセマンティックの種類に関連付けることができます。

> [!div class="mx-imgBorder"]
> ![Health NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[関係抽出](#tab/relation-extraction)

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名と時間を関連付けることによって検出されます。 

> [!div class="mx-imgBorder"]
> ![Health RE](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

エンティティ リンク設定では、テキストで言及されている説明されている固有表現を概念の定義済みデータベースで見つかった概念に関連付けることによって、個別のエンティティが明確にされます。 たとえば、統一医療言語システム (UMLS) などです。

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics for Health では、統一医療言語システム ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) メタシソーラス知識ソースにある保健衛生と生物医学のボキャブラリへのリンクがサポートされています。

#### <a name="negation-detection"></a>[否定検出](#tab/negation-detection) 

医療コンテンツの意味は、否定などの修飾子によって大きな影響を受け、誤って診断されると重大な影響を及ぼす可能性があります。 Text Analytics for Health では、テキストに記載されているさまざまなエンティティに対する否定の検出がサポートされています。 

> [!div class="mx-imgBorder"]
> ![Health NEG](../media/ta-for-health/health-negation.png)

---

サポートされているすべてのエンティティの一覧については、Text Analytics for Health によって返される[エンティティのカテゴリ](../named-entity-types.md?tabs=health)を参照してください。

## <a name="supported-languages"></a>サポートされている言語

Text Analytics for Health では、英語のドキュメントのみがサポートされます。

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、[Cognitive Services コンテナー要求フォーム](https://aka.ms/cognitivegate)に記入して送信します。 現在のところ、Text Analytics for Health の使用には課金されません。 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>コンテナーをインストールする

コンテナーをインストールして実行するには、複数の方法があります。 

- [Azure portal](text-analytics-how-to-install-containers.md?tabs=healthcare) を使用して Text Analytics リソースを作成し、Docker を使用してコンテナーを取得します。
- 次の PowerShell と [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) のスクリプトを使用して、リソース デプロイ コンテナーの構成を自動化します。

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers を使用してコンテナーをインストールする

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) は、クラウドでコンテナーを実行する専用の Azure リソースです。 自動スケーリング、Docker コンテナーと Docker Compose のサポート、HTTPS のサポートなど、すぐに使用できる機能があります。

> [!NOTE]
> Azure Web アプリを使用すると、ドメインが自動的に `<appservice_name>.azurewebsites.net` の形式で取得されます

Azure CLI を使用してこの PowerShell スクリプトを実行し、HTTPS 経由でサブスクリプションとコンテナー イメージを使用して、Web App for Containers を作成します。 スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure コンテナー インスタンスを使用してコンテナーをインストールする

Azure コンテナー インスタンス (ACI) を使用して、デプロイを容易にすることもできます。 ACI は、サーバーレスなマネージド Azure 環境内で Docker コンテナーをオンデマンドで実行できるリソースです。 

Azure portal を使用して ACI リソースをデプロイする手順については、[Azure Container Instances の使用方法](text-analytics-how-to-use-container-instances.md)に関する記事を参照してください。 また、Azure CLI を使用して次の PowerShell スクリプトを実行し、コンテナー イメージを使用してサブスクリプションに ACI を作成することもできます。  スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。  ACI リソースあたりの CPU の最大数に制限があるため、要求あたり 5 個を超える大きいドキュメント (それぞれ約 5000 文字) を送信することが予想される場合は、このオプションを選択しないでください。
可用性の情報については、[ACI リージョンのサポート](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability)に関する記事を参照してください。 

> [!NOTE] 
> Azure Container Instances には、組み込みドメインに対する HTTPS のサポートは含まれません。 HTTPS が必要な場合は、証明書の作成とドメインの登録など、手動で構成する必要があります。 この手順については、以下の NGINX を参照してください。

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>ACI の接続をセキュリティで保護する

既定では、コンテナー API で ACI を使用するときに、セキュリティは提供されません。 これは、通常、コンテナーはネットワーク ブリッジによって外部から保護されるポッドの一部として実行されるためです。 ただし、前面にあるコンポーネントを使用してコンテナーを変更し、コンテナー エンドポイントを非公開にしておくこともできます。 次の例では、[NGINX](https://www.nginx.com) をイングレス ゲートウェイとして使用し、HTTPS/SSL およびクライアント証明書認証をサポートします。

> [!NOTE]
> NGINX は、オープンソースで高パフォーマンスの HTTP サーバーとプロキシです。 NGINX コンテナーは、1 つのコンテナーに対する TLS 接続を終端するために使用できます。 さらに複雑な NGINX イングレス ベースの TLS 終端ソリューションも可能です。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX をイングレス ゲートウェイとして設定する

NGINX では、[構成ファイル](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)を使用して、実行時に機能を有効にします。 別のサービスに対する TLS 終端を有効にするには、TLS 接続を終端するための SSL 証明書と、サービスのアドレスを指定するための `proxy_pass` を、指定する必要があります。 以下ではサンプルを提供します。


> [!NOTE]
> `ssl_certificate` では、NGINX コンテナーのローカル ファイル システム内でパスが指定されている必要があります。 `proxy_pass` に指定するアドレスは、NGINX コンテナーのネットワーク内から使用できる必要があります。

NGINX コンテナーにより、`/etc/nginx/conf.d/` にマウントされている `_.conf_` 内のすべてのファイルが HTTP 構成パスに読み込まれます。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker Compose ファイルの例

次の例では、[Docker Compose](https://docs.docker.com/compose/reference/overview) ファイルを作成して、NGINX と Text Analytics for Health のコンテナーをデプロイする方法を示します。

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

この Docker Compose ファイルを開始するには、ファイルのルート レベルでコンソールから次のコマンドを実行します。

```bash
docker-compose up
```

詳細については、[NGINX SSL 終端](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)に関する NGINX のドキュメントを参照してください。


## <a name="example-api-request"></a>API 要求の例
コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

次の cURL 要求の例を使用し、`serverURL` 変数を適切な値に置き換えて、デプロイしたコンテナーにクエリを送信します。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

次の JSON は、Text Analytics for Health API 要求の POST 本文に添付されている JSON ファイルの例です。

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API 応答の本文

次の JSON は、Text Analytics for Health API 応答本文の例です。

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>否定検出の出力

否定検出を使用する場合、1 つの否定語句によって一度に複数の用語が処理される場合があります。 認識されたエンティティの否定は、JSON 出力では `isNegated` フラグのブール値によって表されます。

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>関係抽出の出力

関係抽出の出力には、関係の "*ソース*" とその "*ターゲット*" への URI 参照が含まれています。 関係ロールが `ENTITY` のエンティティは、`target` フィールドに割り当てられます。 関係ロールが `ATTRIBUTE` のエンティティは、`source` フィールドに割り当てられます。 省略形の関係には、双方向の `source` と `target` フィールドが含まれ、`bidirectional` は `true` に設定されます。 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [名前付きエンティティのカテゴリ](../named-entity-types.md)
* [新機能](../whats-new.md)
