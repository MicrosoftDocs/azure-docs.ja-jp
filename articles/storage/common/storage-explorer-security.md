---
title: Azure Storage Explorer セキュリティ ガイド |Microsoft Docs
description: Azure Storage Explorer のセキュリティ ガイダンス
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783761"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage Explorer セキュリティ ガイド

Microsoft Azure Storage Explorer を使用することで、Windows、macOS、および Linux で Azure Storage データを容易に操作することができます。 これらのガイドラインに従うことで、データを確実に保護することができます。

## <a name="general"></a>全般

- **常に最新バージョンの Storage Explorer を使用する。** Storage Explorer のリリースには、セキュリティ更新プログラムが含まれることがあります。 最新の状態を維持することで、一般的なセキュリティを確保できます。
- **信頼できるリソースにのみ接続する。** 信頼されていないソースからダウンロードしたデータは悪意がある可能性があり、また信頼できないソースにデータをアップロードすると、データが失われるか、盗まれる可能性があります。
- **可能な場合は常に HTTPS を使用する。** Storage Explorer では、既定で HTTPS が使用されます。 一部のシナリオでは HTTP を使用できますが、HTTP は最後の手段としてのみ使用すべきです。
- **必要なアクセス許可だけが、必要なユーザーに与えられていることを確認する。** リソースへのアクセス許可を付与する際は、必要以上の許可を与えないようにします。
- **重要な操作を実行する際は注意して行う。** 削除や上書きなどの特定の操作は元に戻すことができないため、データ損失が発生する可能性があります。 これらの操作を実行する前に、正しいリソースを使用していることを確認してください。

## <a name="choosing-the-right-authentication-method"></a>適切な認証方法の選択

Storage Explorer には、Azure Storage リソースにアクセスするためのさまざまな方法が用意されています。 どの方法を選択した場合でも、推奨事項は以下のとおりです。

### <a name="azure-ad-authentication"></a>Azure AD 認証

Azure Storage リソースにアクセスする最も簡単で安全な方法は、Azure アカウントでサインインすることです。 サインインには、以下を可能にする Azure AD 認証が使用されます。

- 特定のユーザーとグループにアクセス権を付与する。
- 特定のユーザーとグループへのアクセスをいつでも取り消す。
- 多要素認証の要求など、アクセス条件を強制する。

可能な限り Azure AD 認証を使用することをお勧めします。

このセクションでは、ストレージ リソースをセキュリティで保護するために使用できる 2 つの Azure AD ベースのテクノロジについて説明します。

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御 (Azure RBAC)

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure リソースに対するきめ細かいアクセス制御を提供します。 Azure のロールとアクセス許可は、Azure portal から管理できます。

Storage Explorer では、ストレージ アカウント、BLOB、およびキューへの Azure RBAC アクセスがサポートされています。 ファイル共有またはテーブルにアクセスする必要がある場合は、ストレージ アカウント キーを一覧表示するアクセス許可を付与する Azure ロールを割り当てる必要があります。

#### <a name="access-control-lists-acls"></a>アクセス制御リスト (ACL)

[アクセス制御リスト (ACL)](../blobs/data-lake-storage-access-control.md) を使用すると、ADLS Gen2 BLOB コンテナーでファイルおよびフォルダー レベルのアクセスを制御できます。 Storage Explorer を使用して ACL を管理できます。

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)

Azure AD 認証を使用できない場合は Shared Access Signatures を使用することをお勧めします。 Shared Access Signatures を使用すると、次のことを行うことができます。

- セキュリティで保護されたリソースへの匿名の制限付きアクセスを提供する。
- SAS が共有アクセス ポリシー (SAP) から生成された場合に、直ちにこれを取り消す。

ただし、Shared Access Signatures を使用して、次のことを行うことはできません。

- SAS を使用できるユーザーを制限する。 有効な SAS は、所持していれば誰でも使用できます。
- SAS が共有アクセス ポリシー (SAP) から生成されていない場合に、これを取り消す。

Storage Explorer で SAS を使用する場合は、次のガイドラインに従うことをお勧めします。

- **SAS トークンと URI の分布を制限する。** SAS トークンと URI は、信頼されたユーザーにのみ配布します。 SAS 配布を制限すると、SAS が誤用される可能性が低くなります。
- **信頼できるエンティティからの SAS トークンと URI のみを使用する。**
- **可能な場合、SAS トークンと URI を生成する際は共有アクセス ポリシー (SAP) を使用する。** 共有アクセス ポリシーに基づく SAS は、SAS そのものよりも安全性が高くなります。これは、SAP を削除することで、SAS が失効するためです。
- **トークンを生成する際は最小限のリソース アクセスとアクセス許可を付与する。** 付与するアクセス許可を最小限にすることで、SAS が誤用された場合に発生する可能性のある損害を抑えます。
- **必要な期間だけ有効なトークンを生成する。** SAS そのものを生成後に失効させることはできないため、有効期間は特に重要です。

> [!IMPORTANT]
> サービス要求やバグ レポートなど、トラブルシューティングの目的で SAS トークンと URI を共有する場合は、必ず少なくとも SAS の署名部分を墨消しします。

### <a name="storage-account-keys"></a>ストレージ アカウント キー

ストレージ アカウント キーは、ストレージ アカウント内のサービスとリソースへの無制限のアクセスを付与します。 このため、Storage Explorer のリソースにアクセスする際は、キーの使用を制限することをお勧めします。 代わりに、Azure RBAC 機能または SAS を使用してアクセスを提供します。

一部の Azure ロールによって、ストレージ アカウント キーを取得するためのアクセス許可が付与されます。 これらのロールを持つユーザーは、Azure RBAC によるアクセス許可の付与または拒否を事実上回避できます。 必要な場合を除き、このアクセス許可を付与しないことをお勧めします。

Storage Explorer は、利用可能な場合、リクエストの認証にストレージ アカウント キーを使用しようとします。 この機能は、設定 ( **[サービス] > [ストレージ アカウント] > [Disable Usage of Keys]\(キーの使用を無効にする\)** ) から無効にできます。 従来のストレージ アカウントの操作などの一部の機能では、Azure RBAC はサポートされていません。 これらの機能には引き続きキーが必要であり、この設定の影響は受けません。

ストレージ リソースにアクセスするためにキーを使用する必要がある場合は、次のガイドラインに従うことをお勧めします。

- **アカウント キーを他のユーザーと共有しない。**
- **ストレージ アカウント キーは、パスワードのように扱う。** キーをアクセス可能にする必要がある場合は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) などのセキュリティで保護されたストレージ ソリューションを使用します。

> [!NOTE]
> ストレージ アカウント キーが誤って共有または配布されたと思われる場合は、Azure portal からストレージ アカウントの新しいキーを生成できます。

### <a name="public-access-to-blob-containers"></a>BLOB コンテナーへのパブリック アクセス

Storage Explorer を使用すると、Azure Blob Storage コンテナーのアクセス レベルを変更できます。 プライベートでない BLOB コンテナーでは、誰でも匿名でコンテナー内のデータを読み取ることができます。

BLOB コンテナーのパブリック アクセスを有効にする場合は、次のガイドラインに従うことをお勧めします。

- **機密データが含まれている可能性がある BLOB コンテナーでパブリック アクセスを有効にしない。** BLOB コンテナーに、プライベート データが存在しないことを確認します。
- **BLOB またはコンテナー アクセスを持つ BLOB コンテナーに、機密である可能性があるデータをアップロードしない。** 

## <a name="next-steps"></a>次の手順

- [セキュリティに関する推奨事項](../blobs/security-recommendations.md)