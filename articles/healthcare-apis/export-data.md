---
title: Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する
description: この記事では、$export を使用して FHIR データをエクスポートする方法について説明します
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 3437c8bcf8ff508149abae2549d7c34521700840
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627265"
---
# <a name="how-to-export-fhir-data"></a>FHIR データをエクスポートする方法


一括エクスポート機能を使用すると、[FHIR 仕様](https://hl7.org/fhir/uv/bulkdata/export/index.html)に従って FHIR サーバーからデータをエクスポートできます。 

$export を使用する前に、Azure API for FHIR がそれを使用するように構成されていることを確認する必要があります。 エクスポート設定を構成し、Azure ストレージ アカウントを作成する方法については、[データのエクスポートの構成ページ](configure-export-data.md)を参照してください。

## <a name="using-export-command"></a>$export コマンドの使用

エクスポート用に Azure API for FHIR を構成すると、$export コマンドを使用して、サービスからデータをエクスポートできます。 データは、エクスポートの構成時に指定したストレージ アカウントに格納されます。 FHIR サーバーで $export コマンドを呼び出す方法については、[HL7 FHIR $export の仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html)に関するドキュメントを参照してください。 

Azure API For FHIR では、次のレベルでの $export がサポートされています。
* [システム](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者のグループ*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) - Azure API for FHIR では、関連するすべてのリソースがエクスポートされますが、グループの特性はエクスポートされません: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

データがエクスポートされると、リソースの種類ごとに個別のファイルが作成されます。 エクスポートされるファイルが大きくなりすぎないようにするために、エクスポートされた 1 つのファイルのサイズが 64 MB を超えたら新しいファイルを作成します。 この結果、リソースの種類ごとに複数のファイルが取得され、列挙されます (つまり、Patient-1.ndjson、Patient-2.ndjson)。 


> [!Note] 
> リソースが複数のリソースから成るコンパートメント内にある場合、または複数のグループに存在する場合は、`Patient/$export` と `Group/[ID]/$export` によって重複するリソースがエクスポートされることがあります。

さらに、キュー登録中に場所ヘッダーによって返された URL を介したエクスポートの状態の確認と、現行のエクスポート ジョブのキャンセルもサポートされています。



## <a name="settings-and-parameters"></a>設定とパラメーター

### <a name="headers"></a>ヘッダー
$export ジョブに設定する必要がある必須のヘッダー パラメーターが 2 つあります。 値は、現在の [$export 仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)で定義されています。
* **Accept** - application/fhir+json
* **Prefer** - respond-async

### <a name="query-parameters"></a>クエリ パラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        | FHIR 仕様で定義されている    |  説明|
|------------------------|---|------------|
| \_outputFormat | はい | 現在、FHIR 仕様に合わせた 3 つの値 (application/fhir+ndjson、application/ndjson、または単にndjson) がサポートされています。 すべてのエクスポート ジョブは `ndjson` を返し、渡された値はコードの動作に影響を与えません。 |
| \_since | はい | 指定された時間以降に変更されたリソースのみをエクスポートできます |
| \_type | はい | どの種類のリソースを含めるかを指定できます。 たとえば、\_type=Patient とすると、患者のリソースのみが返されます|
| \_typefilter | はい | よりきめ細かいフィルター処理を要求するには、\_typefilter を \_type パラメーターと共に使用します。 _TypeFilter パラメーターの値は、結果をさらに限定する FHIR クエリのコンマ区切りリストです。 |
| \_container | いいえ |  データのエクスポート先となる、構成済みストレージ アカウント内のコンテナーを指定します。 コンテナーが指定されている場合は、その名前を持つ新しいフォルダー内のそのコンテナーにデータがエクスポートされます。 コンテナーが指定されていない場合は、タイムスタンプとジョブ ID を使用して新しいコンテナーにエクスポートされます。 |


## <a name="next-steps"></a>次の手順

この記事では、$export コマンドを使用して、FHIR リソースをエクスポートする方法について説明しました。 次は、匿名化データをエクスポートする方法を確認します。
 
>[!div class="nextstepaction"]
>[匿名化データをエクスポートする](de-identified-export.md)
