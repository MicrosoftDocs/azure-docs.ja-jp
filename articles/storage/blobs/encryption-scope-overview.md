---
title: BLOB ストレージの暗号化スコープ (プレビュー)
description: 暗号化スコープは、コンテナーまたは個々の BLOB のレベルで暗号化を管理する機能を提供します。 暗号化スコープを使用すると、異なる顧客が所有する、同じストレージ アカウントに存在するデータの間にセキュリティで保護された境界を作成できます。
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211364"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>BLOB ストレージの暗号化スコープ (プレビュー)

暗号化スコープは、コンテナーまたは個々の BLOB のレベルで暗号化を管理する機能を提供します。 暗号化スコープを使用すると、異なる顧客が所有する、同じストレージ アカウントに存在するデータの間にセキュリティで保護された境界を作成できます。

既定では、ストレージ アカウントは、そのストレージ アカウント全体をスコープとするキーで暗号化されます。 暗号化スコープを使用すると、1 つ以上のコンテナーが、それらのコンテナーのみをスコープとするキーで暗号化されるように指定できます。

Microsoft のマネージド キーまたは Azure Key Vault に格納されているカスタマー マネージド キーのどちらを使用するかを選択し、データを暗号化するキーへのアクセスを保護および制御できます。 同じストレージ アカウントに対する異なる暗号化スコープで、Microsoft のマネージドまたはカスタマー マネージド キーのいずれかを使用できます。

暗号化スコープを作成したら、コンテナーまたは BLOB を作成するための要求に対してその暗号化スコープを指定できます。 暗号化スコープの作成方法の詳細については、「[暗号化スコープの作成と管理 (プレビュー)](encryption-scope-manage.md)」を参照してください。

> [!IMPORTANT]
> 暗号化スコープは現在、**プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> 予期しないコストを回避するには、現在必要ではない暗号化スコープを必ず無効にしてください。
>
> 暗号化スコープはプレビューの期間中、読み取りアクセス geo 冗長ストレージ (RA-GRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) アカウントではサポートされません。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>暗号化スコープを持つ BLOB またはコンテナーを作成する

暗号化スコープを指定して作成された BLOB は、そのスコープに指定されたキーで暗号化されます。 BLOB を作成するときに個々の BLOB に暗号化スコープを指定するか、コンテナーを作成するときに既定の暗号化スコープを指定できます。 コンテナーのレベルで既定の暗号化スコープを指定すると、そのコンテナー内のすべての BLOB は、既定のスコープに関連付けられているキーで暗号化されます。

既定の暗号化スコープを持つコンテナー内に BLOB を作成すると、既定の暗号化スコープをオーバーライドする暗号化スコープを指定できます (既定の暗号化スコープのオーバーライドを許可するようにコンテナーが構成されている場合)。 既定の暗号化スコープがオーバーライドされないようにするには、個々の BLOB のオーバーライドを拒否するようにコンテナーを構成します。

暗号化スコープに属している BLOB に対する読み取り操作は、暗号化スコープが無効になっていない限り、透過的に行われます。

## <a name="disable-an-encryption-scope"></a>暗号化スコープを無効にする

暗号化スコープを無効にすると、暗号化スコープで実行される後続の読み取りまたは書き込み操作は、HTTP エラー コード 403 (禁止) で失敗します。 暗号化スコープを再度有効にすると、読み取りと書き込みの操作は再び正常に続行されます。

暗号化スコープが無効の場合、それに対して課金されることはなくなります。 不要な料金が発生しないように、不要な暗号化スコープを無効にします。

暗号化スコープがカスタマー マネージド キーで保護されている場合は、暗号化スコープを無効にするために、キー コンテナー内にある関連付けられたキーを削除することもできます。 カスタマー マネージド キーはキー コンテナー内での論理的な削除と消去保護によって保護されており、削除されたキーはそれらのプロパティによって定義されている動作に従うことにご注意ください。 詳細については、Azure Key Vault のドキュメントで次のトピックのいずれかを参照してください。

- [PowerShell で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)
- [CLI で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> 暗号化スコープを削除することはできません。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](../common/storage-service-encryption.md)
- [暗号化スコープの作成と管理 (プレビュー)](encryption-scope-manage.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](../common/customer-managed-keys-overview.md)
- [Azure Key Vault とは](../../key-vault/general/overview.md)