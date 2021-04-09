---
title: Azure Data Lake Storage Gen2 で JavaScript を使用して ACL を設定する
description: JavaScript 用の Azure Storage Data Lake クライアント ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントでアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 4d3e13c6593c0e11df84131a9a07eb2868277d2f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653706"
---
# <a name="use-javascript-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で JavaScript を使用して ACL を管理する

この記事では、JavaScript を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、更新する方法について説明します。 

[パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="set-up-your-project"></a>プロジェクトの設定

ターミナル ウィンドウを開いてから次のコマンドを入力して、JavaScript 用の Data Lake クライアント ライブラリをインストールします。

```javascript
npm install @azure/storage-file-datalake
```

このステートメントをコード ファイルの先頭に配置して、`storage-file-datalake` パッケージをインポートします。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」を参照してください。

[JS 用 Azure ID クライアント ライブラリ](https://www.npmjs.com/package/@azure/identity) を使用して、Azure AD でアプリケーションを認証できます。

クライアント ID、クライアント シークレット、およびテナント ID を取得します。 これを行うには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) ロールをセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。  

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> その他の例については、[JS 用 Azure ID クライアント ライブラリ](https://www.npmjs.com/package/@azure/identity)のドキュメントを参照してください。

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> この承認方法は、Node.js アプリケーションに対してのみ機能します。 ブラウザーでコードを実行する予定の場合、Azure Active Directory (AD) を使用して承認できます。

## <a name="get-and-set-a-directory-acl"></a>ディレクトリの ACL を取得して設定する

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`/`) を渡します。

### <a name="get-and-set-a-file-acl"></a>ファイルの ACL を取得して設定する

この例では、`upload-file.txt` という名前のファイルの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>関連項目

- [パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)