---
title: Azure リソースの Azure Active Directory 管理 ID を持つ blob およびキューへのアクセスの認証 - Azure Storage |Microsoft Docs
description: Azure の Blob およびキュー ストレージは、Azure リソースの管理 ID を使用する Azure Active Directory 認証をサポートします。 Azure リソースの管理 ID を使用して、Azure の仮想マシン、関数アプリ、仮想マシン スケール セット、およびその他においてで実行されているアプリケーションから blob およびキューへのアクセスを認証することができます。 Azure リソースの管理 ID を使用し、Azure AD の認証機能を利用して、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dfdb419a5c06dc50717c0a8a3bdaffb302db52d0
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793018"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する

Azure の Blob およびキュー ストレージは、Azure Active Directory (Azure AD) 認証を[ Azure リソースの管理 ID ](../../active-directory/managed-identities-azure-resources/overview.md)を使用してサポートします。 Azure リソースに対するマネージド ID により、Azure の仮想マシン (VM)、関数アプリ、仮想マシン スケール セットなどで実行されているアプリケーションから BLOB およびキューへのアクセスを、Azure AD 資格情報を使用して認証することができます。 Azure リソースの管理 ID を使用し、Azure AD の認証機能を利用して、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。  

管理 ID から blob コンテナーまたはキューへのアクセス許可を付与するには、ロールベースのアクセス制御 (RBAC) ロールを適切なスコープでそのリソースに対するアクセス許可を含む管理 ID に割り当てます。 ストレージの RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。 

この記事では Azure VM から 管理 ID を使用して Azure Blob または キューストレージ の認証を受ける方法について説明しています。  

## <a name="enable-managed-identities-on-a-vm"></a>VM 上の管理 ID を有効にする

VM から blob およびキューへのアクセス認証に対し Azure リソースの管理 ID を使用する前に、まずは VM 上の管理 ID を有効にする必要があります。 Azure リソースの管理 ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Azure AD のマネージド ID に RBAC ロールを割り当てる

Azure Storage アプリケーションからマネージド ID の認証を行うには、最初に、そのマネージド ID に対してロールベースのアクセス制御 (RBAC) の設定を構成します。 コンテナーとキューのアクセス許可を含む RBAC ロールは、Azure Storage によって定義されます。 マネージド ID に RBAC ロールを割り当てると、そのマネージド ID にそのリソースへのアクセス権が付与されます。 詳細については、[RBAC を使用した Azure BLOB とキューのデータへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。

## <a name="get-a-managed-identity-access-token"></a>管理 ID アクセス トークンの取得

管理 ID を使用して認証するには、アプリケーションまたはスクリプトは、管理 ID へのアクセス トークンを取得する必要があります。 アクセス トークンを取得する方法については、「[アクセストークンを取得するために Azure VM 上の Azure リソースの管理 ID を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)」を参照してください。

OAuth トークンを使用して BLOB とキューの操作を承認するには、HTTPS を使用する必要があります。

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB を作成する

このコード例では、管理 ID アクセス トークンがあることを前提としています。 アクセス トークンは、管理 ID によるブロック BLOB の作成を承認するために使用されます。

### <a name="add-references-and-using-statements"></a>参照と using ステートメントを追加する  

Visual Studio で、Azure Storage クライアント ライブラリをインストールします。 **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。 次のコマンドをコンソールに入力します。

```powershell
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

以下の using ステートメントをコードに追加します。

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>管理 ID アクセス トークンから資格情報を作成する

ブロック BLOB を作成するには、**TokenCredentials** クラスを使用します。 以前取得した管理 ID アクセス トークンを渡して、**TokenCredentials** の新しいインスタンスを作成します。

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD と Azure Storage の統合では、Azure Storage 操作に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
- Azure AD ID で Azure CLI と PowerShell にサインインする方法については、[CLI または PowerShell での Azure AD ID を使用した Azure Storage へのアクセス](storage-auth-aad-script.md)に関するページをご覧ください。