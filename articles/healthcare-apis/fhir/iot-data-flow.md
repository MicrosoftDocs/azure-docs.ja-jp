---
title: '概念: Azure API for FHIR の Azure IoT Connector for FHIR (プレビュー) 機能のデータ フロー'
description: Azure IoT Connector for FHIR (プレビュー) のデータ フローについて説明します。 Azure IoT Connector for FHIR (プレビュー) は、IoMT データの取り込み、正規化、グループ化、変換、および Azure API for FHIR への保存を行います。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: dc20d4cc54824fc65eba6669eab9a1c32d7ef9eb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018626"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Azure IoT Connector for FHIR (プレビュー) のデータ フロー

この記事では、Azure IoT Connector for Fast Healthcare Interoperability Resources (FHIR&#174;)* のデータ フローの概要について説明します。 デバイス データを FHIR ベースの [Observation](https://www.hl7.org/fhir/observation.html) リソースに変換する、Azure IoT Connector for FHIR 内のさまざまなデータ処理段階について説明します。

![Azure IoT Connector for FHIR のデータ フロー](media/concepts-iot-data-flow/iot-connector-data-flow.png)

上の図は、Azure IoT Connector for FHIR を使用した一般的なデータ フローを示しています。 

以下は、Azure IoT Connector for FHIR によって受信されたデータが経由するさまざまな段階です。

## <a name="ingest"></a>取り込み
取り込みは、デバイス データが Azure IoT Connector for FHIR に受信される最初の段階です。 デバイス データのインジェスト エンドポイントは、[Azure Event Hubs](../../event-hubs/index.yml) でホストされます。 Azure Event Hubs プラットフォームは、1 秒あたり数百万件のメッセージを受信して処理する機能により、高いスケールおよびスループットを実現します。 また、これを使用すると、Azure IoT Connector for FHIR でメッセージを非同期に使用できるようになり、デバイス データの処理中にデバイスの待機が不要になります。

> [!NOTE]
> 現時点でデバイス データの形式としてサポートされているは JSON のみです。

## <a name="normalize"></a>Normalize (正規化)
次の段階である正規化では、デバイス データが上記の Azure Event Hubs から取得され、デバイス マッピング テンプレートを使用して処理されます。 このマッピング プロセスにより、デバイス データが、正規化されたスキーマに変換されます。 

正規化プロセスによって、後の段階でのデータ処理が簡素化されるだけでなく、1 つの入力メッセージを複数の正規化されたメッセージに投影することもできます。 たとえば、デバイスから 1 つのメッセージで、体温、脈拍数、血圧、および呼吸速度に関する複数の生命兆候を送信できます。 この入力メッセージから、4 つの別個の FHIR リソースが作成されます。 各リソースは異なる生命兆候を表し、入力メッセージが 4 つの異なる正規化されたメッセージに投影されます。

## <a name="group"></a>グループ
次の段階であるグループ化では、デバイス ID、測定タイプ、期間の 3 つの異なるパラメーターを使用して、前の段階から入手できる正規化されたメッセージがグループ化されます。

デバイス ID と測定タイプのグループ化により、[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 測定タイプを使用できるようになります。 このタイプは、FHIR 内のデバイスから収集した時間ベースの一連の測定値を表す簡潔な方法を提供します。 また、期間により、Azure IoT Connector for FHIR によって生成された Observation リソースが Azure API for FHIR に書き込まれるときの待機時間が制御されます。

> [!NOTE]
> 期間の既定値は 15 分に設定されており、プレビュー用に構成することはできません。

## <a name="transform"></a>変換
変換の段階では、グループ化および正規化されたメッセージは FHIR マッピング テンプレートを使用して処理されます。 テンプレートの種類に一致するメッセージは、マッピングで指定されているとおりに FHIR ベースの Observation リソースに変換されます。

また、この時点で、[Device](https://www.hl7.org/fhir/device.html) リソースとそれに関連付けられている [Patient](https://www.hl7.org/fhir/patient.html) リソースが、メッセージに含まれるデバイス識別子を使用して FHIR サーバーから取得されます。 これらのリソースは、作成される Observation リソースへの参照として追加されます。

> [!NOTE]
> FHIR サーバーの負荷を軽減するために、すべての ID 参照は一度解決されるとキャッシュされます。 複数の患者に対してデバイスの再利用を予定している場合は、その患者に固有の仮想デバイス リソースを作成し、メッセージ ペイロードで仮想デバイス識別子を送信することをお勧めします。 仮想デバイスは、実際のデバイス リソースに親としてリンクさせることができます。

特定のデバイス識別子の Device リソースが FHIR サーバーに存在しない場合、結果は作成時に設定された `Resolution Type` の値によって決まります。 `Lookup` に設定すると、特定のメッセージは無視され、パイプラインは他の受信メッセージの処理を続行します。 `Create` に設定すると、Azure IoT Connector for FHIR によって、FHIR サーバー上に必要最小限の Device および Patient リソースが作成されます。  

## <a name="persist"></a>保持
変換の段階で Observation FHIR リソースが生成されると、リソースは Azure API for FHIR に保存されます。 FHIR リソースが新しい場合は、サーバー上に作成されます。 FHIR リソースが既に存在している場合は、更新されます。

## <a name="next-steps"></a>次のステップ

デバイスおよび FHIR のマッピング テンプレートを作成する方法については、次のステップをクリックしてください。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR のマッピング テンプレート](iot-mapping-templates.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。 