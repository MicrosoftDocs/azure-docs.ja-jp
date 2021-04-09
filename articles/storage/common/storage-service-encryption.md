---
title: 保存データに対する Azure Storage 暗号化
description: Azure Storage では、クラウドに永続化される前にデータを自動的に暗号化することによって、データが保護されます。 Microsoft のマネージド キーを利用してストレージ アカウント内のデータを暗号化することも、独自のキーで暗号化を管理することもできます。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b2471ccd2a412c7cbae9d4e59412ac055697e3d7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180362"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>保存データに対する Azure Storage 暗号化

Azure Storage では、サーバー側暗号化 (SSE) を使用して、データがクラウドに永続化されるときに自動的に暗号化します。 Azure Storage 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-azure-storage-encryption"></a>Azure Storage 暗号化について

Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。

Azure Storage 暗号化は、Resource Manager と従来のストレージ アカウントの両方を含む、すべてのストレージ アカウントで有効になります。 Azure Storage 暗号化を無効にすることはできません。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。

ストレージ アカウント内のデータは、パフォーマンス レベル (Standard または Premium)、アクセス層 (ホットまたはクール)、デプロイ モデル (Azure Resource Manager またはクラシック) に関係なく、暗号化されます。 アーカイブ層のすべての BLOB も暗号化されます。 すべての Azure Storage 冗長性オプションは暗号化をサポートし、geo レプリケーションが有効になっている場合は、プライマリとセカンダリ両方のリージョンのすべてのデータが暗号化されます。 BLOB、ディスク、ファイル、キュー、テーブルなど、すべての Azure Storage リソースが暗号化されます。 すべてのオブジェクト メタデータも暗号化されます。 Azure Storage 暗号化に対する追加コストはありません。

2017 年 10 月 20 日より後に Azure Storage に書き込まれたすべてのブロック BLOB、追加 BLOB、またはページ BLOB は暗号化されます。 この日付より前に作成された BLOB は、引き続きバックグラウンド プロセスによって暗号化されます。 2017 年 10 月 20 日より前に作成された BLOB を強制的に暗号化するには、BLOB を書き換えることができます。 BLOB の暗号化状態を確認する方法については、「[BLOB の暗号化状態を確認する](../blobs/storage-blob-encryption-status.md)」を参照してください。

Azure Storage 暗号化の基になっている暗号化モジュールについて詳しくは、「[Cryptography API: Next Generation (暗号化 API: 次世代)](/windows/desktop/seccng/cng-portal)」を参照してください。

Azure マネージド ディスクの暗号化とキー管理に関する詳細については、「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/disk-encryption.md)」のページを参照してください。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

既定では、新しいストレージ アカウント内のデータは Microsoft マネージド キーで暗号化されます。 引き続き Microsoft マネージド キーを利用してデータを暗号化することも、独自のキーで暗号化を管理することもできます。 独自のキーで暗号化を管理する場合は、次の 2 つのオプションがあります。 どちらかの種類のキー管理またはその両方を使用できます。

- BLOB ストレージと Azure Files のデータの暗号化と復号化に使用する "*カスタマーマネージド キー*" を指定できます。<sup>1、2</sup> カスタマーマネージド キーは、Azure Key Vault または Azure Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納する必要があります。 カスタマーマネージド キーの詳細については、[Azure Storage 暗号化へのカスタマーマネージド キーの使用](./customer-managed-keys-overview.md)に関するページを参照してください。
- BLOB ストレージの操作では、"*カスタマー指定のキー*" を指定できます。 BLOB ストレージ に対して読み取りまたは書き込み要求を行うクライアントは、BLOB データの暗号化と暗号化解除の方法を細かく制御するために、要求に暗号化キーを含めることができます。 カスタマー指定のキーの詳細については、「[BLOB ストレージに対する要求で暗号化キーを指定する](../blobs/encryption-customer-provided-keys.md)」を参照してください。

次の表では、Azure Storage 暗号化のキー管理オプションを比較しています。

| キー管理パラメーター | Microsoft のマネージド キー | カスタマー マネージド キー | カスタマー指定のキー |
|--|--|--|--|
| 暗号化/暗号化解除の操作 | Azure | Azure | Azure |
| サポートされている Azure Storage サービス | All | BLOB ストレージ、Azure Files<sup>1、2</sup> | BLOB ストレージ |
| キー記憶域 | Microsoft キー ストア | Azure Key Vault または Key Vault HSM | お客様独自のキー ストア |
| キーのローテーションの責任 | Microsoft | Customer | Customer |
| キー コントロール | Microsoft | Customer | Customer |

<sup>1</sup> Queue storage でのカスタマー マネージド キーの使用をサポートするアカウントの作成については、[キューのカスタマー マネージド キーをサポートするアカウントの作成](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)に関するページを参照してください。<br />
<sup>2</sup> Table Storage でのカスタマー マネージド キーの使用をサポートするアカウントの作成については、[テーブルのカスタマー マネージド キーをサポートするアカウントの作成](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)に関するページを参照してください。

> [!NOTE]
> Microsoft マネージド キーは、コンプライアンス要件に応じて適切に交換されます。 特定のキー交換の要件がある場合は、ユーザーが自分で交換を管理および監査できるように、カスタマー マネージド キーに移行することをお勧めします。

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>インフラストラクチャの暗号化を使用してデータを二重に暗号化する

データのセキュリティ保護について高いレベルの保証が必要な顧客は、Azure Storage インフラストラクチャ レベルで 256 ビットの AES 暗号化を有効にすることもできます。 インフラストラクチャ暗号化が有効な場合、ストレージ アカウントのデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 &mdash; サービス レベルで 1 回、インフラストラクチャ レベルで 1 回 &mdash; 暗号化されます。 Azure Storage データの二重暗号化を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 このシナリオでは、追加の暗号化レイヤーによって引き続きデータが保護されます。

サービスレベルの暗号化では、Azure Key Vault での Microsoft マネージド キーまたはカスタマー マネージド キーの使用をサポートしています。 インフラストラクチャレベルの暗号化は Microsoft マネージド キーに依存し、常に別のキーが使用されます。

インフラストラクチャの暗号化を有効にするストレージ アカウントを作成する方法の詳細については、「[データの二重暗号化のためにインフラストラクチャ暗号化を有効にしてストレージ アカウントを作成する](infrastructure-encryption-enable.md)」を参照してください。

## <a name="encryption-scopes-for-blob-storage-preview"></a>BLOB ストレージの暗号化スコープ (プレビュー)

既定では、ストレージ アカウントは、そのストレージ アカウントにスコープが設定されたキーで暗号化されます。 Microsoft のマネージド キーまたは Azure Key Vault に格納されているカスタマー マネージド キーのどちらを使用するかを選択し、データを暗号化するキーへのアクセスを保護および制御できます。

暗号化スコープを使用すると、必要に応じて、コンテナーまたは個々の BLOB のレベルで暗号化を管理できます。 暗号化スコープを使用すると、異なる顧客が所有する、同じストレージ アカウントに存在するデータの間にセキュリティで保護された境界を作成できます。

Azure Storage リソース プロバイダーを使用して、ストレージ アカウントに 1 つ以上の暗号化スコープを作成できます。 暗号化スコープを作成するときに、Microsoft のマネージド キーまたは Azure Key Vault に格納されているカスタマー マネージド キーのどちらでスコープを保護するか指定します。 同じストレージ アカウントに対する異なる暗号化スコープで、Microsoft のマネージドまたはカスタマー マネージド キーのいずれかを使用できます。

暗号化スコープを作成したら、コンテナーまたは BLOB を作成するための要求に対してその暗号化スコープを指定できます。 暗号化スコープの作成方法の詳細については、「[暗号化スコープの作成と管理 (プレビュー)](../blobs/encryption-scope-manage.md)」を参照してください。

> [!NOTE]
> プレビュー段階では、読み取りアクセスの geo 冗長ストレージ (RA-GRS) アカウントと、読み取りアクセスの geo ゾーン冗長ストレージ (RA-GZRS) アカウントを使用した暗号化スコープはサポートされていません。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 暗号化スコープのプレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。
>
> 予期しないコストを回避するには、現在必要ではない暗号化スコープを必ず無効にしてください。

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>暗号化スコープを持つ BLOB またはコンテナーを作成する

暗号化スコープを指定して作成された BLOB は、そのスコープに指定されたキーで暗号化されます。 BLOB を作成するときに個々の BLOB に暗号化スコープを指定するか、コンテナーを作成するときに既定の暗号化スコープを指定できます。 コンテナーのレベルで既定の暗号化スコープを指定すると、そのコンテナー内のすべての BLOB は、既定のスコープに関連付けられているキーで暗号化されます。

既定の暗号化スコープを持つコンテナー内に BLOB を作成すると、既定の暗号化スコープをオーバーライドする暗号化スコープを指定できます (既定の暗号化スコープのオーバーライドを許可するようにコンテナーが構成されている場合)。 既定の暗号化スコープがオーバーライドされないようにするには、個々の BLOB のオーバーライドを拒否するようにコンテナーを構成します。

暗号化スコープに属している BLOB に対する読み取り操作は、暗号化スコープが無効になっていない限り、透過的に行われます。

### <a name="disable-an-encryption-scope"></a>暗号化スコープを無効にする

暗号化スコープを無効にすると、暗号化スコープで実行される後続の読み取りまたは書き込み操作は、HTTP エラー コード 403 (禁止) で失敗します。 暗号化スコープを再度有効にすると、読み取りと書き込みの操作は再び正常に続行されます。

暗号化スコープが無効の場合、それに対して課金されることはなくなります。 不要な料金が発生しないように、不要な暗号化スコープを無効にします。

暗号化スコープが Azure Key Vault のカスタマー マネージド キーで保護されている場合は、暗号化スコープを無効にするために、キー コンテナー内の関連付けられているキーを削除することもできます。 Azure Key Vault 内のカスタマー マネージド キーは論理的な削除と消去保護によって保護されており、削除されたキーには、それらのプロパティによって定義されている動作が適用されることに注意してください。 詳細については、Azure Key Vault のドキュメントで次のトピックのいずれかを参照してください。

- [PowerShell で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)
- [CLI で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> 暗号化スコープを削除することはできません。

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault とは](../../key-vault/general/overview.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
- [BLOB ストレージの暗号化スコープ (プレビュー)](../blobs/encryption-scope-overview.md)
