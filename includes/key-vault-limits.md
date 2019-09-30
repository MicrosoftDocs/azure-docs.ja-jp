---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181580"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>キーのトランザクション (リージョンあたりのコンテナーごとに、10 秒間に許可される最大トランザクション数<sup>1</sup>):

|キーの種類|HSM キー<br>CREATE キー|HSM キー<br>その他すべてのトランザクション|ソフトウェア キー<br>CREATE キー|ソフトウェア キー<br>その他すべてのトランザクション|
|:---|---:|---:|---:|---:|
|RSA 2,048 ビット|5|1,000|10|2,000|
|RSA 3,072 ビット|5|250|10|500|
|RSA 4,096 ビット|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 前の表を見ると、RSA 2,048 ビット ソフトウェア キーでは、10 秒間に 2,000 件の GET トランザクションが許可されます。 RSA 2,048 ビット HSM キーでは、10 秒間に 1,000 件の GET トランザクションが許可されます。
>
> スロットルのしきい値は重み付けされ、合計に対して適用されます。 たとえば、前の表で示されているように、RSA HSM キーで GET 操作を実行する場合に 4,096 ビット キーを使用すると、2,048 ビット キーと比較して 8 倍のコストがかかります。 これは、1,000/125 = 8 から導き出されます。
>
> 10 秒間に、Azure Key Vault クライアントは、`429` のスロットル HTTP 状態コードが発生するまで、次の操作のいずれか *1 つのみ*を実行できます。
> - 2,000 件の RSA 2,048 ビット ソフトウェア キー GET トランザクション
> - 1,000 件の RSA 2,048 ビット HSM キー GET トランザクション
> - 125 件の RSA 4,096 ビット HSM キー GET トランザクション
> - 124 件の RSA 4,096 ビット HSM キー GET トランザクションおよび 8 件の RSA 2,048 ビット HSM キー GET トランザクション

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>シークレット、管理ストレージ アカウント キー、およびコンテナーのトランザクション:
| トランザクションの種類 | リージョンあたりのコンテナーごとに、10 秒間に許可される最大トランザクション数<sup>1</sup> |
| --- | --- |
| すべてのトランザクション |2,000 |

これらの制限を超えたときにスロットルを処理する方法については、「[Azure Key Vault のスロットル ガイダンス](../articles/key-vault/key-vault-ovw-throttling.md)」をご覧ください。

<sup>1</sup> すべてのトランザクションの種類に適用されるサブスクリプション レベルの制限は、キー コンテナーの制限の 5 倍です。 たとえば、1 つのサブスクリプションで許可される HSM - その他のトランザクションの最大数は、10 秒間に 5,000 トランザクションです。
