---
title: Azure API for FHIR の匿名化データ (プレビュー) をエクスポートする
description: この記事では、匿名化エクスポートを設定して使用する方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019190"
---
# <a name="exporting-de-identified-data-preview"></a>匿名化データをエクスポートする (プレビュー)

> [!Note] 
> 匿名化エクスポートの使用時の結果は、入力されたデータや、顧客が選択した関数などの要因によってさまざまです。 Microsoft では、匿名化エクスポートの出力を評価したり、お客様のユース ケースやコンプライアンスのニーズにおいて受け入れ可能かどうかを判断したりすることはできません。 匿名化エクスポートにより、法律、規制、またはコンプライアンスの特定の要件が満たされるとは保証されません。

$export コマンドを使用して、FHIR サーバーから匿名化データをエクスポートすることもできます。 [FHIR 匿名化ツール](https://github.com/microsoft/FHIR-Tools-for-Anonymization)の匿名化エンジンが使用され、クエリ パラメーターで匿名化構成の詳細が取得されます。 独自の匿名化構成ファイルを作成することも、HIPAA Safe Harbor メソッドの[サンプル構成ファイル](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method)を出発点として使用することもできます。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 現在、Azure API for FHIR では、システム レベルでの匿名化エクスポートのみがサポートされています ($export)。

|Query parameter (クエリ パラメーター)            | 例 |選択肢| 説明|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|匿名化エクスポートで必須 |構成ファイルの名前です。 構成ファイル形式については、[ここ](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)を参照してください。 このファイルは、エクスポート先として構成されている場所と同じ Azure ストレージ アカウント内の **匿名化** という名前のコンテナー内に保存する必要があります。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|匿名化エクスポートで任意|これは構成ファイルの Etag です。 BLOB プロパティから Azure ストレージ エクスプローラーを使用して Etag を取得できます。|

> [!IMPORTANT]
> 生エクスポートと匿名化エクスポートはどちらも、エクスポート構成の一部として指定されたのと同じ Azure ストレージ アカウントに書き込まれます。 別の匿名化構成に対応する別のコンテナーを使用し、コンテナー レベルでユーザー アクセスを管理することをお勧めします。