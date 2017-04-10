---
title: "Azure Government のセキュリティと ID | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security--identity"></a>Azure Government のセキュリティと ID
## <a name="key-vault"></a>Key Vault
このサービスとその使用方法について詳しくは、[Azure Key Vault のパブリック ドキュメント](../key-vault/index.md)をご覧ください。

### <a name="data-considerations"></a>データに関する考慮事項
以下の情報は、Azure Key Vault に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Azure Key Vault キーで暗号化されるすべてのデータには、規制対象データが含まれていてもかまいません。 |Azure Key Vault のメタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、Key Vault を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  リソース グループ名、Key Vault 名、サブスクリプション名の各フィールドには規制対象データを入力しないでください。 |

Key Vault は、Azure Government で一般提供されています。 パブリックと同様、拡張機能は存在しません。Key Vault の利用は、PowerShell と CLI のみとなります。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。




<!--HONumber=Nov16_HO3-->


