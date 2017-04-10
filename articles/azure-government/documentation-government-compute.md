---
title: "Azure Government のコンピューティング | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 146fcd9788bab55e4abd9e70dcfe25741f041d34
ms.lasthandoff: 03/15/2017


---
# <a name="azure-government-compute"></a>Azure 政府機関向けコンピューティング
## <a name="virtual-machines"></a>Virtual Machines
このサービスとその使用方法について詳しくは、「[Azure の仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

### <a name="variations"></a>バリエーション
次の製品 (VM) が Azure Government で一般公開されています。

| 製品 (VM) | 米国政府バージニア州 | 米国政府アイオワ州 | 米国防総省東部 | 米国防総省西部
| --- | --- | --- |--- |--- |
| A0-A7 |Y |Y |Y |Y |Y |
| Av2 |Y |Y |Y |Y |Y |
| D シリーズ |Y |N |N |N |
| Dv2 シリーズ |Y |Y |Y |Y |
| DS シリーズ |Y |N |N |N |
| DSv2 シリーズ |Y |N |Y |Y |
| F シリーズ |Y |Y |Y |Y |
| FS シリーズ |Y |N |Y |Y |
| G シリーズ |Y |N |N |N |
| GS シリーズ |Y |N |N |N |

### <a name="data-considerations"></a>データに関する考慮事項
以下の情報は、Azure Virtual Machines に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| VM 内で入力、保存、処理されるデータには、輸出規制対象データが含まれていてもかまいません。 Azure Virtual Machines 内で実行されるバイナリ。 静的な認証コード (Azure のプラットフォーム コンポーネントにアクセスするためのパスワード、スマートカードの PIN など)。 Azure のプラットフォーム コンポーネントを管理する際に使用する証明書の秘密キー。 SQL 接続文字列。  その他、Azure サービスに格納される各種セキュリティ情報/シークレット (証明書、暗号化キー、マスター キー、ストレージ キーなど)。 |メタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、Azure Virtual Machines を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  テナント ロール名、リソース グループ、デプロイ名、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。 |

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。


