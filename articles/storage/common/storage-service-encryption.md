---
title: 保存データに対する Azure Storage 暗号化
description: Azure Storage では、クラウドに永続化される前にデータを自動的に暗号化することによって、データが保護されます。 Microsoft のマネージド キーを利用してストレージ アカウントを暗号化することも、独自のキーで暗号化を管理することもできます。
services: storage
author: tamram
ms.service: storage
ms.date: 12/05/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a09d2c0c2a393acd4882842dc023b0f5f682e813
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895126"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>保存データに対する Azure Storage 暗号化

Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 Azure Storage 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-azure-storage-encryption"></a>Azure Storage 暗号化について

Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。

Azure Storage 暗号化は、Resource Manager と従来のストレージ アカウントの両方を含む、すべての新しいストレージ アカウントで有効になります。 Azure Storage 暗号化を無効にすることはできません。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。

ストレージ アカウントは、そのパフォーマンス レベル (Standard または Premium) またはデプロイ モデル (Azure Resource Manager またはクラシック) に関係なく、暗号化されます。 Azure Storage のすべての冗長性オプションで暗号化がサポートされており、ストレージ アカウントのすべてのコピーが暗号化されます。 BLOB、ディスク、ファイル、キュー、テーブルなど、すべての Azure Storage リソースが暗号化されます。 すべてのオブジェクト メタデータも暗号化されます。

暗号化により、Azure Storage のパフォーマンスが影響を受けることはありません。 Azure Storage 暗号化に対する追加コストはありません。

2017 年 10 月 20 日より後に Azure Storage に書き込まれたすべてのブロック BLOB、追加 BLOB、またはページ BLOB は暗号化されます。 この日付より前に作成された BLOB は、引き続きバックグラウンド プロセスによって暗号化されます。 2017 年 10 月 20 日より前に作成された BLOB を強制的に暗号化するには、BLOB を書き換えることができます。 BLOB の暗号化状態を確認する方法については、「[BLOB の暗号化状態を確認する](../blobs/storage-blob-encryption-status.md)」を参照してください。

Azure Storage 暗号化の基になっている暗号化モジュールについて詳しくは、「[Cryptography API: Next Generation (暗号化 API: 次世代)](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

Microsoft のマネージド キーを利用してストレージ アカウントを暗号化することも、独自のキーで暗号化を管理することもできます。 独自のキーで暗号化を管理する場合は、次の 2 つのオプションがあります。

- Azure Key Vault では、BLOB ストレージや Azure Files 内のデータを暗号化および暗号化解除するために使用する*カスタマー マネージド キー*を指定できます。
- BLOB ストレージの操作では、"*カスタマー指定のキー*" を指定できます。 BLOB ストレージ に対して読み取りまたは書き込み要求を行うクライアントは、BLOB データの暗号化と暗号化解除の方法を細かく制御するために、要求に暗号化キーを含めることができます。

次の表では、Azure Storage 暗号化のキー管理オプションを比較しています。

|                                        |    Microsoft のマネージド キー                             |    カスタマー マネージド キー                                                                                                                        |    カスタマー指定のキー                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    暗号化/暗号化解除の操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    サポートされている Azure Storage サービス    |    All                                                |    BLOB ストレージ、Azure Files                                                                                                               |    BLOB ストレージ                                                                  |
|    キー記憶域                         |    Microsoft キー ストア    |    Azure Key Vault                                                                                                                              |    Azure Key Vault またはその他のキー ストア                                                                 |
|    キーのローテーションの責任         |    Microsoft                                          |    顧客                                                                                                                                     |    顧客                                                                      |
|    キー使用法                           |    Microsoft                                          |    Azure portal、Storage Resource Provider REST API、Azure Storage 管理ライブラリ、PowerShell、CLI        |    Azure Storage REST API (BLOB ストレージ)、Azure Storage クライアント ライブラリ    |
|    キーへのアクセス                          |    Microsoft のみ                                     |    Microsoft、顧客                                                                                                                    |    顧客のみ                                                                 |

以降のセクションでは、キー管理の各オプションについてさらに詳しく説明します。

## <a name="microsoft-managed-keys"></a>Microsoft のマネージド キー

既定では、ストレージ アカウントには Microsoft のマネージド暗号化キーが使われます。 ストレージ アカウントの暗号化の設定は、次の図のように、[Azure portal](https://portal.azure.com) の **[暗号化]** セクションで確認できます。

![Microsoft のマネージド キーで暗号化されたアカウントを表示する](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

独自のキーを使用して、ストレージ アカウントのレベルで Azure Storage 暗号化を管理できます。 ストレージ アカウントのレベルでカスタマー マネージド キーを指定すると、そのキーは、そのストレージ アカウント内のすべての BLOB およびファイル データを暗号化および暗号化解除するために使用されます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/key-vault-overview.md)」をご覧ください。

次の図は、Azure Storage で Azure Active Directory と Azure Key Vault を使用して、カスタマー マネージド キーを使って要求を行う方法を示しています。

![Azure Storage でのカスタマー マネージド キーのしくみを示す図](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

次の一覧では、図の番号付きの手順について説明します。

1. Azure Key Vault 管理者が、ストレージ アカウントに関連付けられているマネージド ID に暗号化キーへのアクセス許可を付与します。
2. Azure Storage 管理者が、ストレージ アカウントのカスタマー マネージド キーを使用して暗号化を構成します。
3. Azure Storage は、ストレージ アカウントに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。
4. Azure Storage は、Azure Key Vault のカスタマー キーを使用してアカウント暗号化キーをラップします。
5. 読み取り/書き込み操作の場合、Azure Storage は Azure Key Vault に要求を送信して、暗号化と暗号化解除の操作を実行するためにアカウント暗号化キーをラップおよびラップ解除します。

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>ストレージ アカウントのカスタマー マネージド キーを有効にする

ストレージ アカウントのカスタマー マネージド キーを使用した暗号化を有効にすると、Azure Storage は、関連するキー コンテナーのカスタマー キーを使用してアカウント暗号化キーをラップします。 カスタマー マネージド キーを有効にしてもパフォーマンスに影響はなく、アカウントは新しいキーですぐに暗号化され、時間の遅延は発生しません。

新しいストレージ アカウントは、常に Microsoft のマネージド キーを使用して暗号化されます。 アカウントの作成時にカスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは Azure Key Vault に格納されます。この Key Vault には、ストレージ アカウントに関連するマネージド ID にキーのアクセス許可を付与するアクセス ポリシーをプロビジョニングする必要があります。 マネージド ID は、ストレージ アカウントが作成された後でのみ使用できます。

Azure Storage 暗号化用に Azure Key Vault でカスタマー マネージド キーを使用する方法については、次のいずれかの記事を参照してください。

- [Azure portal から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID がストレージ アカウントに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはストレージ アカウントを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのストレージ アカウントに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

ストレージ アカウントでカスタマー マネージド キーを有効にするには、Azure Key Vault を使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。

Key Vault は、ストレージ アカウントと同じサブスクリプションに配置する必要があります。 Azure Storage は、暗号化および暗号化解除の操作のために、Azure リソースのマネージド ID を使用して Key Vault に対して認証を行います。 マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。

### <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーをローテーションするときは、新しいキー URI を使用するようにストレージ アカウントを更新する必要があります。 Azure portal で新しいバージョンのキーを使用するようにストレージ アカウントを更新する方法については、「[Azure ポータルを使用して、Azure Storage 用にカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)」の「**キーのバージョンを更新する**」というタイトルのセクションを参照してください。

キーをローテーションしても、ストレージ アカウントのデータの再暗号化はトリガーされません。 ユーザーがこれ以上操作を行う必要はありません。

### <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](/powershell/module/az.keyvault//) に関する記事、または [Azure Key Vault CLI](/cli/azure/keyvault) に関する記事を参照してください。 アクセスを取り消すと、Azure Storage が暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全データへのアクセスが事実上ブロックされます。

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Azure マネージド ディスク用のカスタマー マネージド キー (プレビュー)

カスタマー マネージド キーは、Azure マネージド ディスクの暗号化の管理にも使用できます (プレビュー)。 マネージド ディスクに対するカスタマー マネージド キーの動作は、Azure Storage リソースに対する動作とは異なります。 詳細については、Windows の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/windows/disk-encryption.md)」を参照するか、Linux の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/linux/disk-encryption.md)」を参照してください。

## <a name="customer-provided-keys-preview"></a>カスタマー指定のキー (プレビュー)

Azure BLOB ストレージに対して要求を行うクライアントには、個々の要求に対して暗号化キーを指定するオプションがあります。 要求に暗号化キーを含めると、BLOB ストレージ操作の暗号化設定をきめ細かく制御できます。 カスタマー指定のキー (プレビュー) は、Azure Key Vault または別のキー ストアに格納できます。

BLOB ストレージへの要求時にカスタマー指定のキーを指定する方法の例については、「[.NET での BLOB ストレージの要求時にカスタマー指定のキーを指定する](../blobs/storage-blob-customer-provided-key.md)」を参照してください。 

### <a name="encrypting-read-and-write-operations"></a>読み取り操作と書き込み操作の暗号化

クライアント アプリケーションが要求に対して暗号化キーを指定すると、Azure Storage は BLOB データの読み取りおよび書き込み中に暗号化と暗号化解除を透過的に実行します。 Azure Storage は、暗号化キーの SHA-256 ハッシュを BLOB の内容と共に書き込みます。 このハッシュを使用して、BLOB に対する後続のすべての操作で同じ暗号化キーが使用されているか確認されます。 

Azure Storage は、クライアントが要求と共に送信する暗号化キーの格納も管理も行いません。 暗号化または暗号化解除のプロセスが完了するとすぐに、キーは安全に破棄されます。

クライアントがカスタマー指定のキーを使用して BLOB を作成または更新した場合は、その BLOB に対する以降の読み取り要求と書き込み要求でもそのキーを指定する必要があります。 カスタマー指定のキーで既に暗号化されている BLOB の要求にキーが指定されていない場合、要求はエラーコード 409 (競合) で失敗します。

クライアント アプリケーションが要求で暗号化キーを送信し、ストレージ アカウントも Microsoft のマネージド キーまたはカスタマー マネージド キーを使用して暗号化されている場合、Azure Storage は、要求で指定されたキーを暗号化と暗号化解除に使用します。

暗号化キーを要求の一部として送信するために、クライアントは HTTPS を使用して Azure Storage へのセキュリティで保護された接続を確立する必要があります。

各 BLOB スナップショットには、独自の暗号化キーを保持できます。

### <a name="request-headers-for-specifying-customer-provided-keys"></a>カスタマー指定のキーを指定するための要求ヘッダー

REST 呼び出しの場合、クライアントは次のヘッダーを使用して、要求の暗号化キー情報を BLOB ストレージに安全に渡すことができます。

|要求ヘッダー | 説明 |
|---------------|-------------|
|`x-ms-encryption-key` |書き込み要求と読み取り要求の両方に必要です。 Base64 でエンコードされた AES-256 暗号化キー値です。 |
|`x-ms-encryption-key-sha256`| 書き込み要求と読み取り要求の両方に必要です。 暗号化キーの Base64 でエンコードされた SHA256 です。 |
|`x-ms-encryption-algorithm` | 書き込み要求の場合は必須、読み取り要求の場合は省略可能です。 指定されたキーを使用してデータを暗号化するときに使用するアルゴリズムを指定します。 AES256 である必要があります。 |

要求に対する暗号化キーの指定は省略できます。 ただし、上記のいずれかのヘッダーを書き込み操作に指定する場合は、それらのすべてを指定する必要があります。

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>カスタマー指定のキーをサポートしている BLOB ストレージ操作

次の BLOB ストレージ操作では、要求でカスタマー指定の暗号化キーを送信することをサポートしています。

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block from URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>カスタマー指定のキーのローテーション

要求で渡される暗号化キーをローテーションするには、BLOB をダウンロードし、新しい暗号化キーを使用して再アップロードします。

> [!IMPORTANT]
> Azure portal を使用して、要求で指定されたキーで暗号化されたコンテナーまたは BLOB への読み取りおよび書き込みを行うことはできません。
>
> BLOB ストレージに対する要求で指定した暗号化キーは、Azure Key Vault などのセキュリティで保護されたキー ストア内で必ず保護してください。 暗号化キーを使用せずにコンテナーまたは BLOB に対して書き込み操作を試みると、操作は失敗し、そのオブジェクトにアクセスできなくなります。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage 暗号化とディスク暗号化

Azure Storage 暗号化は、Azure 仮想マシン ディスクをバックアップするページ BLOB を暗号化します。 さらに、すべての Azure 仮想マシンのディスクは、ローカル一時ディスクを含め、[Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) を使用して暗号化することもできます。 Azure Disk Encryption では、業界標準である [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) (Windows 上) および [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) (Linux 上) が使われており、Azure Key Vault と統合されたオペレーティング システム ベースの暗号化ソリューションが提供されます。

## <a name="next-steps"></a>次の手順

- [Azure Key Vault とは](../../key-vault/key-vault-overview.md)
- [Azure portal から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-cli.md)
