---
title: Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する
description: この記事では、$export を使用して FHIR データをエクスポートする方法について説明します
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559198"
---
# <a name="how-to-export-fhir-data"></a>FHIR データをエクスポートする方法


一括エクスポート機能を使用すると、[FHIR 仕様](https://hl7.org/fhir/uv/bulkdata/export/index.html)に従って FHIR サーバーからデータをエクスポートできます。 

$Export を使用する前に、FHIR 用の Azure API がそれを使用するように構成されていることを確認する必要があります。 エクスポート設定を構成し、Azure ストレージ アカウントを作成する方法については、[データのエクスポートの構成ページ](configure-export-data.md)を参照してください。

## <a name="using-export-command"></a>$export コマンドの使用

エクスポート用に Azure API for FHIR を構成すると、$export コマンドを使用して、サービスからデータをエクスポートできます。 データは、エクスポートの構成時に指定したストレージ アカウントに格納されます。 FHIR サーバーで $export コマンドを呼び出す方法については、[HL7 FHIR $export の仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html)に関するドキュメントを参照してください。


**ジョブが無効な状態でスタックした**

状況によっては、ジョブが正しくない状態でスタックする可能性があります。 これは特に、ストレージアカウントのアクセス許可が適切に設定されていない場合に発生する可能性があります。 エクスポートが成功したかどうかを検証する1つの方法として、ストレージアカウントを調べて、対応するコンテナー (つまり、ndjson) ファイルが存在するかどうかを確認します。 存在しない場合、他のエクスポートジョブが実行されていないと、現在のジョブが正常な状態でスタックしていない可能性があります。 キャンセル要求を送信してエクスポートジョブをキャンセルしてから、ジョブを再度キューに登録してください。 エクスポートが無効な状態になった場合の既定の実行時間は、停止して新しいジョブに移動するか、またはエクスポートを再試行するまで10分です。 

Azure API For FHIR では、次のレベルでの $export がサポートされています。
* [システム](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者のグループ *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -FHIR 用の Azure API は、関連するすべてのリソースをエクスポートしますが、グループの特性はエクスポートしません。 `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

データがエクスポートされると、リソースの種類ごとに個別のファイルが作成されます。 エクスポートされたファイルが大きくなりすぎないようにするため。 エクスポートされた1つのファイルのサイズが 64 MB を超えると、新しいファイルが作成されます。 その結果、リソースの種類ごとに複数のファイルが取得され、列挙されます (つまり、患者-1. ndjson、患者-2. ndjson)。 


> [!Note] 
> リソースが複数のリソースから成るコンパートメント内にある場合、または複数のグループに存在する場合は、`Patient/$export` と `Group/[ID]/$export` によって重複するリソースがエクスポートされることがあります。

さらに、キュー登録中に場所ヘッダーによって返された URL を介したエクスポートの状態の確認と、現行のエクスポート ジョブのキャンセルもサポートされています。

### <a name="exporting-fhir-data-to-adls-gen2"></a>FHIR データを ADLS Gen2 にエクスポートする

現在、ADLS Gen2 対応のストレージ アカウントに対する $export がサポートされていますが、次の制限があります。

- ユーザーは [階層的な名前空間](../../storage/blobs/data-lake-storage-namespace.md)を利用できませんが、コンテナー内の特定のサブディレクトリへのエクスポートをターゲットにする方法はありません。 ターゲットにすることができるのは、特定のコンテナーだけです (エクスポートごとに新しいフォルダーが作成されます)。
- エクスポートが完了すると、そのフォルダーに再び何かがエクスポートされることはありません。同じコンテナーへの後続のエクスポートは新しく作成されたフォルダーに入れられるためです。


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

> [!Note]
> $Export 操作の宛先として登録できるのは、FHIR 用の Azure API の場合と同じサブスクリプション内のストレージアカウントだけです。

## <a name="secure-export-to-azure-storage"></a>Azure Storage へのセキュア エクスポート

Azure API for FHIR では、セキュア エクスポート操作がサポートされます。 セキュア エクスポートを実行する方法の 1 つは、Azure API for FHIR に関連付けられた特定の IP アドレスに Azure ストレージ アカウントへのアクセスを許可することです。 ストレージ アカウントが Azure API for FHIR と同じ場所にあるか別の場所にあるかによって、構成が異なります。

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Azure ストレージ アカウントが別のリージョンにある場合

ポータルから Azure ストレージアカウントの [ **ネットワーク** ] を選択します。 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure Storage の [ネットワーク] の設定。" lightbox="media/export-data/storage-networking.png":::
   
**[選択されたネットワーク]** を選択します。 [ファイアウォール] セクションで、[ **アドレス範囲** ] ボックスに IP アドレスを指定します。 インターネットまたはオンプレミスネットワークからのアクセスを許可する IP 範囲を追加します。 次の表では、Azure API for FHIR サービスがプロビジョニングされている Azure リージョンの IP アドレスを確認できます。

|**Azure リージョン**         |**パブリック IP アドレス** |
|:----------------------|:-------------------|
| オーストラリア東部       | 20.53.44.80       |
| カナダ中部       | 20.48.192.84      |
| 米国中部           | 52.182.208.31     |
| 米国東部              | 20.62.128.148     |
| 米国東部 2            | 20.49.102.228     |
| 米国東部 2 EUAP       | 20.39.26.254      |
| ドイツ北部        | 51.116.51.33      |
| ドイツ中西部 | 51.116.146.216    |
| 東日本           | 20.191.160.26     |
| 韓国中部        | 20.41.69.51       |
| 米国中北部     | 20.49.114.188     |
| 北ヨーロッパ         | 52.146.131.52     |
| 南アフリカ北部   | 102.133.220.197   |
| 米国中南部     | 13.73.254.220     |
| 東南アジア       | 23.98.108.42      |
| スイス北部    | 51.107.60.95      |
| 英国南部             | 51.104.30.170     |
| 英国西部              | 51.137.164.94     |
| 米国中西部      | 52.150.156.44     |
| 西ヨーロッパ          | 20.61.98.66       |
| 米国西部 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Azure ストレージ アカウントが同じリージョンにある場合

構成プロセスは上記と同じですが、CIDR 形式の特定の IP アドレス範囲 100.64.0.0/10 が代わりに使用される点が異なります。 IP アドレス範囲 (100.64.0.0 – 100.127.255.255 を含む) を指定する必要がある理由は、サービスで使用される実際の IP アドレスは $export 要求ごとに異なりますが、この範囲には入ることを示すためです。

> [!Note] 
> 10.0.2.0/24 の範囲内のプライベート IP アドレスを代わりに使用することができます。 この場合、$export 操作は成功しません。 $Export 要求を再試行することはできますが、100.64.0.0/10 の範囲内の IP アドレスが次回使用されるという保証はありません。 これは設計上の既知のネットワーク動作です。 代替策として、ストレージ アカウントを別のリージョンで構成する方法があります。
    
## <a name="next-steps"></a>次の手順

この記事では、$export コマンドを使用して、FHIR リソースをエクスポートする方法について説明しました。 次に、特定できないデータをエクスポートする方法については、以下を参照してください。
 
>[!div class="nextstepaction"]
>[匿名化データをエクスポートする](de-identified-export.md)
