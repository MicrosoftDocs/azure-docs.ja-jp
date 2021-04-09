---
title: Azure API for FHIR のデータ変換
description: $convert-data エンドポイントとコンバーターのカスタマイズ テンプレートを使用して、Azure API for FHIR のデータを変換します。
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 2a34cfee57ecc1870c420c4c0f3c9261aa02f192
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490927"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>データを FHIR に変換する方法 (プレビュー)

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure API for FHIR の $convert-data カスタム エンドポイントは、各種形式から FHIR へのデータ変換を目的としたものです。 既定のテンプレートとして [FHIR Converter](https://github.com/microsoft/FHIR-Converter) プロジェクトのテンプレートと Liquid テンプレート エンジンが使用されます。 これらの変換テンプレートは必要に応じてカスタマイズすることができます。 現在、HL7v2 から FHIR への変換がサポートされます。

## <a name="use-the-convert-data-endpoint"></a>$convert-data エンドポイントを使用する

`https://<<FHIR service base URL>>/$convert-data`

以下のように、$convert-data は、要求本文で [Parameter](http://hl7.org/fhir/parameters.html) リソースを受け取ります。

**パラメーター リソース:**

| パラメーター名      | 説明 | 指定可能な値 |
| ----------- | ----------- | ----------- |
| inputData      | 変換の対象となるデータ。 | JSON String データ型の有効な値|
| inputDataType   | 入力のデータ型。 | ```HL7v2``` |
| templateCollectionReference | テンプレート コレクションへの参照。 **既定のテンプレート** への参照か、Azure API for FHIR に登録されているカスタム テンプレート イメージへの参照を指定できます。 テンプレートのカスタマイズ、ACR でのテンプレートのホスティング、Azure API for FHIR への登録については、以降を参照してください。  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | データの変換中に使用するルート テンプレート。 | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> 既定のテンプレートを使用すると手間がかかりません。 ただしこれらは、Microsoft が Azure API for FHIR をアップグレードするときに更新される可能性があります。 Azure API for FHIR のバージョンが異なってもデータ変換の動作に一貫性を確保するためには、独自のテンプレートのコピーを Azure Container Registry でホストし、それらを Azure API for FHIR に登録して API 呼び出しに使用する必要があります (後述)。

**要求のサンプル:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**応答のサンプル:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>テンプレートをカスタマイズする

テンプレートは、Visual Studio Code 用の [FHIR Converter 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)を使用して、必要に応じてカスタマイズすることができます。 この拡張機能は、対話型の編集エクスペリエンスを提供するものであり、Microsoft が公開しているテンプレートやサンプル データを簡単にダウンロードできるようになっています。 詳細については、拡張機能内のドキュメントを参照してください。

## <a name="host-and-use-templates"></a>テンプレートをホストして使用する

独自のテンプレートのコピーを ACR でホストすることを強くお勧めします。 独自のテンプレートのコピーをホストし、それらを $convert-data 操作で使用するためには、次の 4 つの手順が必要となります。

1. Azure Container Registry にテンプレートをプッシュします。
1. Azure API for FHIR インスタンスでマネージド ID を有効にします。
1. Azure API for FHIR マネージド ID に ACR のアクセス権を付与します。
1. Azure API for FHIR に ACR サーバーを登録します。

### <a name="push-templates-to-azure-container-registry"></a>Azure Container Registry にテンプレートをプッシュする

ACR インスタンスの作成後、[FHIR Converter 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)の _FHIR Converter:Push Templates_ コマンドを使用して、カスタマイズしたテンプレートを ACR にプッシュします。 同じことは、[Template Management CLI ツール](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md)を使用して行うこともできます。

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR でマネージド ID を有効にする

Azure portal で Azure API for FHIR サービスのインスタンスにアクセスし、 **[ID]** ブレードを選択します。
Azure API for FHIR でマネージド ID を有効にするには、状態を **[オン]** に変更します。

![マネージド ID の有効化](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Azure API for FHIR に ACR のアクセス権を付与する

ACR インスタンスの [アクセス制御 (IAM)] ブレードに移動し、 _[ロールの割り当ての追加]_ を選択します。

![ACR ロールの割り当て](media/convert-data/fhir-acr-role-assignment.png)

Azure API for FHIR サービス インスタンスに AcrPull ロールを付与します。

![ロールの追加](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Azure API for FHIR に ACR サーバーを登録する

Azure portal を使用するか、CLI を使用して、ACR サーバーを登録できます。

#### <a name="registering-the-acr-server-using-azure-portal"></a>Azure portal を使用した ACR サーバーの登録
Azure API for FHIR インスタンスの [_データ変換_] の下にある [_アーティファクト_] ブレードに移動します。 現在登録されている ACR サーバーの一覧が表示されます。 [ _追加_ ] をクリックし、ドロップダウンからレジストリサーバーを選択します。 登録を有効にするには、[ _保存_ ] をクリックする必要があります。 変更を適用してインスタンスを再起動するまでに数分かかる場合があります。

#### <a name="registering-the-acr-server-using-cli"></a>CLI を使用した ACR サーバーの登録
Azure API for FHIR には、最大 20 台の ACR サーバーを登録することができます。

必要に応じて Azure PowerShell から healthcareapis CLI をインストールします。

```powershell
az extension add -n healthcareapis
```

以下の例に従って、Azure API for FHIR に ACR サーバーを登録します。

##### <a name="register-a-single-acr-server"></a>1 台の ACR サーバーを登録する

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>複数台の ACR サーバーを登録する

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>確認

templateCollectionReference パラメーターにテンプレート参照を指定して $convert-data API を呼び出します。

`<RegistryServer>/<imageName>@<imageDigest>`
