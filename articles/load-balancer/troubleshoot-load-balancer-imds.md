---
title: Azure Instance Metadata Service (IMDS) の一般的なエラー コード
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service (IMDS) の一般的なエラーコードと対応する軽減方法の概要
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519186"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>エラー コード:IMDS を使用してロードバランサー情報を取得するときの一般的なエラーコード

この記事では、Azure Instance Metadata Service (IMDS) の使用中に発生する一般的なデプロイ エラーとその解決方法について説明します。

## <a name="error-codes"></a>エラー コード

| エラー コード | エラー メッセージ | 詳細と軽減策 |
| --- | ---------- | ----------------- |
| 400 | 必須のパラメーター "\<ParameterName>" が指定されていません。 要求を修正して再試行してください。 | エラー コードは、不足しているパラメーターがあることを示しています。 </br> 不足しているパラメーターを追加する方法の詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。
| 400 | パラメーター値が許可されていないか、パラメーター "ParameterName" に対してパラメーター値 "\<ParameterValue>" が許可されていません。 要求を修正して再試行してください。 | エラー コードは、要求の形式が正しく構成されていないことを示しています。 </br> 要求本文を修正して再試行を発行する方法の詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。 |
| 400 | 予期しない要求です。 クエリ パラメーターを確認して再試行してください。 | エラー コードは、要求の形式が正しく構成されていないことを示しています。 </br> 要求本文を修正して再試行を発行する方法の詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。 |
| 404 | ロード バランサーのメタデータが見つかりません。 VM が Basic 以外の SKU ロード バランサーを使用しているかどうかを確認してから、後で再試行してください。 | エラー コードは、仮想マシンがロード バランサーに関連付けられていないこと、またはロード バランサーが Standard ではなく Basic SKU であることを示しています。 </br> 詳細については、「[クイック スタート: Azure portal を使用して、VM の負荷分散を行うパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard)」を参照してください。|
| 404 | API が見つかりません:パス = "\<UrlPath>"、メソッド = "\<Method>" | エラー コードは、パスの誤った構成を示しています。 </br> 要求本文を修正して再試行を発行する方法の詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。|
| 405 | Http メソッドは許可されていません:パス = "\<UrlPath>"、メソッド = "\<Method>" | エラー コードは、サポートされていない HTTP 動詞を示しています。 </br> サポートされている動詞の詳細については、[Azure Instance Metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) に関するページを参照してください。 |
| 429 | 要求が多すぎます | エラー コードは、レート制限を示しています。 </br> レート制限の詳細については、[Azure Instance Metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting) に関するページを参照してください。|
| 400 | 要求本文が MaxBodyLength を超えています:... | エラー コードは、MaxBodyLength を超える要求を示しています。 </br> 本文の長さの詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。|
| 400 | パラメーター キーの長さが MaxParameterKeyLength を超えています:... | エラー コードは、パラメーター キーの長さが MaxParameterKeyLength より大きいことを示しています。 </br> 本文の長さの詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。 |
| 400 | パラメーター値の長さが MaxParameterValueLength を超えています:... | エラー コードは、パラメーター キーの長さが MaxParameterValueLength より大きいことを示しています。 </br> 値の長さの詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。|
| 400 | パラメーター ヘッダー値の長さが MaxHeaderValueLength を超えています:... | エラー コードは、パラメーター ヘッダー値の長さが MaxHeaderValueLength より大きいことを示しています。 </br> 値の長さの詳細については、[Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する方法](howto-load-balancer-imds.md#sample-request-and-response)に関するページを参照してください。|
| 404 | Load Balancer メタデータ API は現在使用できません。 後で再試行してください。 | エラー コードは、API がプロビジョニングされている可能性があることを示しています。 後で要求を試してください。 |
| 404 | /metadata/loadbalancer は現在使用できません。 | エラー コードは、API の有効化が進行中であることを示しています。 後で要求を試してください。 |
| 503 | 内部サービスを利用できません。 後で再試行してください。  | エラー コードは、API が一時的に使用できないことを示しています。 後で要求を試してください。 |
|  |  |

## <a name="next-steps"></a>次のステップ

[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) についてさらに詳しく学習する

