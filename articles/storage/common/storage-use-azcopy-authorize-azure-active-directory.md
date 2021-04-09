---
title: AzCopy と Azure Active Directory (Azure AD) を使用して BLOB へのアクセスを承認する | Microsoft Docs
description: AzCopy 操作の認証資格情報は、Azure Active Directory (Azure AD) を使用して提供できます。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/17/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 99e06a36c2afa66f2874c14990d50c6287623efd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672493"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>AzCopy と Azure Active Directory (Azure AD) を使用して BLOB へのアクセスを承認する

Azure AD を使用して、AzCopy に承認資格情報を提供できます。 そうすれば、各コマンドに Shared Access Signature (SAS) トークンを追加する必要がなくなります。 

まず、ロールの割り当てを確認します。 次に、承認する "_セキュリティ プリンシパル_" の種類を選択します。 [ユーザー ID](../../active-directory/fundamentals/add-users-azure-active-directory.md)、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)、および [サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)は、それぞれセキュリティ プリンシパルの種類です。

ユーザー ID は、Azure AD に ID を持つすべてのユーザーです。 これは、承認するのが最も簡単なセキュリティ プリンシパルです。 ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用することを計画している場合、マネージド ID とサービス プリンシパルが最適なオプションです。 Azure 仮想マシン (VM) から実行されるスクリプトには、マネージド ID の方が適しており、オンプレミスで実行されるスクリプトには、サービス プリンシパルの方が適しています。 

AzCopy についての詳細は、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。

## <a name="verify-role-assignments"></a>ロールの割り当てを確認する

必要な認証レベルは、ファイルをアップロードする予定か、ファイルをダウンロードするだけなのかによって異なります。

ファイルをダウンロードするだけの場合は、ユーザー ID、マネージド ID、またはサービス プリンシパルに[ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールが割り当てられていることを確認します。

ファイルをアップロードする場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられていることを確認します。

- [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

これらのロールは、以下のいずれかの範囲でセキュリティ プリンシパルに割り当てることができます。

- コンテナー (ファイル システム)
- ストレージ アカウント
- Resource group
- サブスクリプション

ロールを確認して割り当てる方法については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

> [!NOTE]
> Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。

ターゲット コンテナーまたはディレクトリのアクセス制御リスト (ACL) にご自分のセキュリティ プリンシパルが追加されている場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられている必要はありません。 ACL では、ご自分のセキュリティ プリンシパルには、ターゲット ディレクトリの書き込みアクセス許可と、コンテナーおよび各親ディレクトリの実行アクセス許可が必要になります。

詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](../blobs/data-lake-storage-access-control-model.md)」を参照してください。

## <a name="authorize-a-user-identity"></a>ユーザー ID を承認する

ご自分のユーザー ID に必要な認証レベルが与えられていることを確認したら、コマンド プロンプトを開き、次のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login
```

エラーが発生する場合は、ストレージ アカウントが属する組織のテナント ID を含めてみてください。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![コンテナーを作成する](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、ブラウザー ウィンドウを閉じ、AzCopy の使用を開始できます。

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>マネージド ID を使用して承認する

ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用し、スクリプトが Azure 仮想マシン (VM) から実行される場合、これは優れたオプションです。 このオプションを使用する場合、資格情報を VM に保存する必要はありません。

VM で有効にしたシステム全体のマネージド ID を使用して、または VM に割り当てたユーザー割り当てのマネージド ID のクライアント ID、オブジェクト ID、またはリソース ID を使用して、アカウントにサインインできます。

システム全体のマネージド ID を有効にする方法、またはユーザー割り当てのマネージド ID を作成したりする方法について詳しくは、「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)」をご覧ください。

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>システム全体のマネージド ID を使用して承認する

まず、VM でシステム全体のマネージド ID が有効になっていることを確認します。 「[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)」をご覧ください。

次に、コマンド コンソールで、次のコマンドを入力して Enter キーを押します。

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ID を使用して承認する

まず、VM でユーザー割り当てマネージド ID が有効になっていることを確認します。 「[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)」をご覧ください。

次に、コマンド コンソールで、次のいずれかのコマンドを入力して Enter キーを押します。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のクライアント ID に置き換えます。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のオブジェクト ID に置き換えます。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のリソース ID に置き換えます。

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>サービス プリンシパルを承認する

ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用する予定の場合、これは優れたオプションです (特にオンプレミスで実行するとき)。 Azure で実行されている VM で AzCopy を実行する場合、マネージド サービス ID を管理しやすくなります。 詳しくは、この記事の「[マネージド ID を承認する](#authorize-a-managed-identity)」セクションを参照してください。

そのスクリプトを実行する前に、少なくとも 1 回は対話形式でサインインする必要があります。そうすることで、ご自身のサービス プリンシパルの資格情報を AzCopy に渡すことができます。  それらの資格情報は暗号化された安全なファイルに格納されます。そのため、実際のスクリプトでその機密情報を渡す必要がありません。

クライアント シークレットを使用して、またはご自分のサービス プリンシパルのアプリ登録に関連付けられている証明書のパスワードを使用して、ご自分のアカウントにサインインできます。

サービス プリンシパルの作成の詳細については、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」のガイダンスに従って、サービス プリンシパルを作成します。

サービス プリンシパル全般の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>クライアント シークレットを使用して、サービス プリンシパル承認する

まず、`AZCOPY_SPA_CLIENT_SECRET` 環境変数を、ご自分のサービス プリンシパルのアプリ登録のクライアント シークレットに設定します。

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、これを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、ご自分のパスワードがご使用のコンソールのコマンド履歴に表示されません。  

次に、以下のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

`<application-id>` プレースホルダーを、ご自分のサービス プリンシパルのアプリ登録のアプリケーション ID に置き換えます。 `<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>証明書を使用してサービス プリンシパルを承認する

承認にご自分の資格情報を使用する場合は、ご自分のアプリ登録に証明書をアップロードした後、その証明書を使用してログインできます。

ご自分のアプリ登録にご使用の証明書をアップロードするだけでなく、AzCopy が実行されるマシンまたは VM 上に証明書のコピーを保存する必要もあります。 この証明書のコピーは、.PFX または .PEM 形式で、秘密キーが含まれている必要があります。 秘密キーはパスワードで保護する必要があります。 Windows を使用していて、ご使用の証明書が証明書ストア内にのみ存在する場合は、必ず、その証明書 (秘密キーを含む) を PFX ファイルにエクスポートしてください。 ガイダンスについては、「[Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)」を参照してください。

次に、`AZCOPY_SPA_CERT_PASSWORD` 環境変数を、証明書のパスワードに設定します。

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、このタスクを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

次に、以下のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` プレースホルダーを、証明書ファイルの相対または完全修飾パスに置き換えます。 AzCopy は、この証明書のパスを保存しますが、証明書のコピーは保存しません。そのため、必ず所定の場所にその証明書を保持してください。 `<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、ご自分のパスワードがご使用のコンソールのコマンド履歴に表示されません。 

## <a name="authorize-without-a-secret-store"></a>シークレット ストアなしで承認する

`azcopy login` コマンドによって OAuth トークンが取得され、そのトークンがシステムのシークレット ストアに配置されます。 オペレーティング システムに Linux "*キーリング*" などのシークレット ストアがない場合、トークンを配置する場所がないため、`azcopy login` コマンドは機能しません。 

`azcopy login` コマンドを使用する代わりに、メモリ内環境変数を設定できます。 次に、任意の AzCopy コマンドを実行します。 AzCopy によって、操作を完了するために必要な認証トークンが取得されます。 操作が完了すると、トークンがメモリから消えます。 

### <a name="authorize-a-user-identity"></a>ユーザー ID を承認する

ご自分のユーザー ID に必要な認証レベルが与えられていることを確認したら、次のコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

次に、azcopy コマンド (`azcopy list https://contoso.blob.core.windows.net` など) を実行します。

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![コンテナーを作成する](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、操作は完了です。

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>システム全体のマネージド ID を使用して承認する

まず、VM でシステム全体のマネージド ID が有効になっていることを確認します。 「[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)」をご覧ください。

次のコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

次に、azcopy コマンド (`azcopy list https://contoso.blob.core.windows.net` など) を実行します。

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ID を使用して承認する

まず、VM でユーザー割り当てマネージド ID が有効になっていることを確認します。 「[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)」をご覧ください。

次のコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

その後、次のいずれかのコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

`<client-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のクライアント ID に置き換えます。

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

`<object-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のオブジェクト ID に置き換えます。

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

`<resource-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のリソース ID に置き換えます。

これらの変数を設定したら、任意の azcopy コマンド (`azcopy list https://contoso.blob.core.windows.net` など) を実行することができます。

### <a name="authorize-a-service-principal"></a>サービス プリンシパルを承認する

クライアント シークレットを使用して、またはご自分のサービス プリンシパルのアプリ登録に関連付けられている証明書のパスワードを使用して、ご自分のアカウントにサインインできます。

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>クライアント シークレットを使用して、サービス プリンシパル承認する

次のコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
```

`<application-id>` プレースホルダーを、ご自分のサービス プリンシパルのアプリ登録のアプリケーション ID に置き換えます。 `<client-secret>` プレースホルダーをクライアント シークレットに置き換えます。

> [!NOTE]
> プロンプトを使用して、ユーザーからパスワードを収集することを検討してください。 そうすることで、お使いのパスワードがコマンドの履歴に表示されなくなります。 

次に、azcopy コマンド (`azcopy list https://contoso.blob.core.windows.net` など) を実行します。

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>証明書を使用してサービス プリンシパルを承認する

承認にご自分の資格情報を使用する場合は、ご自分のアプリ登録に証明書をアップロードした後、その証明書を使用してログインできます。

ご自分のアプリ登録にご使用の証明書をアップロードするだけでなく、AzCopy が実行されるマシンまたは VM 上に証明書のコピーを保存する必要もあります。 この証明書のコピーは、.PFX または .PEM 形式で、秘密キーが含まれている必要があります。 秘密キーはパスワードで保護する必要があります。 

次のコマンドを入力して、ENTER キーを押します。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
```

`<path-to-certificate-file>` プレースホルダーを、証明書ファイルの相対または完全修飾パスに置き換えます。 AzCopy は、この証明書のパスを保存しますが、証明書のコピーは保存しません。そのため、必ず所定の場所にその証明書を保持してください。 `<certificate-password>` プレースホルダーを証明書のパスワードに置き換えます。

> [!NOTE]
> プロンプトを使用して、ユーザーからパスワードを収集することを検討してください。 そうすることで、お使いのパスワードがコマンドの履歴に表示されなくなります。 

次に、azcopy コマンド (`azcopy list https://contoso.blob.core.windows.net` など) を実行します。

## <a name="next-steps"></a>次のステップ

- AzCopy についての詳細は、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください

- ご質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) ページからお送りください。