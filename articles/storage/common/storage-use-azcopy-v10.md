---
title: AzCopy v10 を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565794"
---
# <a name="get-started-with-azcopy"></a>AzCopy を使ってみる

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。

> [!NOTE]
> AzCopy **V10** が現在サポートされているバージョンの AzCopy です。
>
> AzCopy **v8.1** を使用する必要がある場合、この記事の「[以前のバージョンの AzCopy の使用](#previous-version)」を参照してください。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy をダウンロードする

まず、お使いのコンピューター上の任意のディレクトリに AzCopy V10 実行可能ファイルをダウンロードします。 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) サービスとの間でデータをコピーする場合、[AzCopy バージョン 7.3](https://aka.ms/downloadazcopynet) をインストールしてください。

## <a name="run-azcopy"></a>AzCopy を実行する

利便性のため、AzCopy 実行可能ファイルのディレクトリの場所をご自分のシステム パスに追加して使いやすくすることを検討してください。 そうすると、ご使用のシステム上にある任意のディレクトリから「`azcopy`」を入力できます。

AzCopy ディレクトリをご自分のパスに追加しないことを選択した場合、実際の AzCopy 実行可能ファイルの場所にディレクトリを変更し、Windows PowerShell コマンド プロンプトで「`azcopy`」または「`.\azcopy`」と入力する必要があります。

コマンドの一覧を表示するには、「`azcopy -h`」と入力し、ENTER キーを押します。

特定のコマンドの情報を知るには、単にコマンドの名前を含めてください (例: `azcopy list -h`)。

![インライン ヘルプ](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> ご自分の Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 AzCopy を使用して意味のある動作を行う前に、ストレージ サービスに認証資格情報を提供する方法を決定する必要があります。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>認証資格情報の提供方法を選択する

認証資格情報は、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用して提供できます。

次の表をガイドとして使用してください。

| ストレージの種類 | 現在サポートされている認証方法 |
|--|--|
|**Blob Storage** | Azure AD および SAS |
|**BLOB ストレージ (階層的名前空間)** | Azure AD および SAS |
|**File Storage** | SAS のみ |

### <a name="option-1-use-azure-ad"></a>オプション 1:Azure AD の使用

Azure AD を使用すると、各コマンドに SAS トークンを追加する代わりに、資格情報を 1 回入力するだけで済みます。  

必要な認証レベルは、ファイルをアップロードする予定か、ファイルをダウンロードするだけなのかによって異なります。

ファイルをダウンロードするだけの場合は、ご自分のユーザー ID またはサービス プリンシパルに[ストレージ BLOB データ閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)が割り当てられていることを確認します。 

> [!NOTE]
> ユーザー ID とサービス プリンシパルはそれぞれ*セキュリティ プリンシパル*の種類です。そのため、この記事の残りの部分では、*セキュリティ プリンシパル*という用語を使用します。

ファイルをアップロードする場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられていることを確認します。

- [ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [ストレージ BLOB データ所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

これらのロールは、以下のいずれかの範囲でご自分の ID に割り当てることができます。

- コンテナー (ファイル システム)
- ストレージ アカウント
- Resource group
- Subscription

ロールを確認し、割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

> [!NOTE] 
> RBAC ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。

ターゲット コンテナーまたはディレクトリのアクセス制御リスト (ACL) にご自分のセキュリティ プリンシパルが追加されている場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられている必要はありません。 ACL では、ご自分のセキュリティ プリンシパルには、ターゲット ディレクトリの書き込みアクセス許可と、コンテナーおよび各親ディレクトリの実行アクセス許可が必要になります。

詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に関するページを参照してください。

#### <a name="authenticate-a-user-identity"></a>ユーザー ID の認証

ご自分のユーザー ID に必要な認証レベルが与えられていることを確認したら、コマンド プロンプトを開き、次のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login
```

1 つ以上の組織に属している場合、ストレージ アカウントが属する組織のテナント ID を含めます。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![コンテナーを作成する](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、ブラウザー ウィンドウを閉じ、AzCopy の使用を開始できます。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>サービス プリンシパルの認証

ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用する予定の場合、これは優れたオプションです。 

そのスクリプトを実行する前に、少なくとも 1 回対話的にサインインする必要があります。そうすることで、ご自分のサービス プリンシパルの資格情報を AzCopy に渡すことができます。  それらの資格情報は暗号化された安全なファイルに格納されます。そのため、実際のスクリプトでその機密情報を渡す必要がありません。

クライアント シークレットを使用して、またはご自分のサービス プリンシパルのアプリ登録に関連付けられている証明書のパスワードを使用して、ご自分のアカウントにサインインできます。 

サービス プリンシパルの作成の詳細については、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)」のガイダンスに従って、サービス プリンシパルを作成します。

サービス プリンシパル全般の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)」を参照してください。

##### <a name="using-a-client-secret"></a>クライアント シークレットの使用

まず、`AZCOPY_SPA_CLIENT_SECRET` 環境変数を、ご自分のサービス プリンシパルのアプリ登録のクライアント シークレットに設定します。 

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、これを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、クライアント シークレットがご使用のコンソールのコマンド履歴に表示されません。 

次に、次のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal --application-id <application-id>
```

`<application-id>` プレースホルダーを、ご自分のサービス プリンシパルのアプリ登録のアプリケーション ID に置き換えます。

##### <a name="using-a-certificate"></a>証明書の使用

承認にご自分の資格情報を使用する場合は、ご自分のアプリ登録に証明書をアップロードした後、その証明書を使用してログインできます。

ご自分のアプリ登録にご使用の証明書をアップロードするだけでなく、AzCopy が実行されるマシンまたは VM 上に証明書のコピーを保存する必要もあります。 この証明書のコピーは、.PFX または .PEM 形式で、秘密キーが含まれている必要があります。 秘密キーはパスワードで保護する必要があります。 Windows を使用していて、ご使用の証明書が証明書ストア内にのみ存在する場合は、必ず、その証明書 (秘密キーを含む) を PFX ファイルにエクスポートしてください。 ガイダンスについては、「[Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)」を参照してください。

次に、`AZCOPY_SPA_CERT_PASSWORD` 環境変数を、証明書のパスワードに設定します。

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、これを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

次に、次のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

`<path-to-certificate-file>` プレースホルダーを、証明書ファイルの相対または完全修飾パスに置き換えます。 AzCopy は、この証明書のパスを保存しますが、証明書のコピーは保存しません。そのため、必ず所定の場所にその証明書を保持してください。

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、ご自分のパスワードがご使用のコンソールのコマンド履歴に表示されません。 

### <a name="option-2-use-a-sas-token"></a>オプション 2:SAS トークンを使用する

AzCopy コマンドで使用する各コピー元または各コピー先の URL に SAS トークンを追加できます。

この例のコマンドでは、ローカル ディレクトリから BLOB コンテナーにデータが繰り返しコピーされます。 架空の SAS トークンがコンテナー URL の末尾に追加されます。

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS トークンの詳細とその取得方法については、「[Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)」を参照してください。

## <a name="transfer-files"></a>ファイルの転送

ID を認証し、SAS トークンを取得したら、ファイルの転送を開始できます。

サンプル コマンドは次の記事のいずれかをご覧ください。

- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy と Azure Stack ストレージを使用してデータを転送する](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>スクリプト内で AzCopy を使用する

そのスクリプトを実行する前に、少なくとも 1 回対話的にサインインする必要があります。そうすることで、ご自分のサービス プリンシパルの資格情報を AzCopy に渡すことができます。  それらの資格情報は暗号化された安全なファイルに格納されます。そのため、実際のスクリプトでその機密情報を渡す必要がありません。 例については、この記事の「[サービス プリンシパルの認証](#service-principal)」セクションを参照してください。

時間と共に、AzCopy の[ダウンロード リンク](#download-and-install-azcopy)は AzCopy の新しいバージョンを指します。 実際のスクリプトで AzCopy をダウンロードする場合、実際のスクリプトで使用する機能が新しいバージョンの AzCopy で変更されていると、スクリプトの動作が停止する可能性があります。 

こうした問題を回避するには、AzCopy の現在のバージョンの静的 (変更されない) リンクを取得します。 そうすることで、実際のスクリプトを実行するたびに、まったく同じバージョンの AzCopy がダウンロードされます。

そのリンクを取得するには、このコマンドを実行します。

| オペレーティング システム  | command |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux の場合、`tar` コマンドの `--strip-components=1` では、バージョン名を含む最上位フォルダーが削除され、代わりに現在のフォルダーに直接バイナリが抽出されます。 これにより、`wget` URL を更新するだけで、新しいバージョンの `azcopy` でスクリプトを更新できます。

この URL はこのコマンドの出力に表示されます。 その後、実際のスクリプトでその URL を使用して AzCopy をダウンロードできます。

| オペレーティング システム  | command |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Storage Explorer で AzCopy を使用する

AzCopy のパフォーマンス上の利点を活用するとき、ファイルの操作に、コマンド ラインではなく Storage Explorer を使用する場合は、ストレージ エクスプローラーで AzCopy を有効にします。 

Storage Explorer で、 **[プレビュー]** -> の **[Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\)** に移動します。

![Azure Storage Explorer で転送エンジンとして AzCopy を有効にする](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> ストレージ アカウントで階層型名前空間を有効にしてある場合、この設定を有効にする必要はありません。 ストレージ エクスプローラーでは、階層型名前空間が与えられているストレージ アカウントで AzCopy が自動的に使用されるためです。  

Storage Explorer では、ご自分のアカウント キーを使用して、操作を実行します。そのため、Storage Explorer にサインインした後は、追加の承認資格情報を提供する必要はありません。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>以前のバージョンの AzCopy の使用

前のバージョンの AzCopy (AzCopy v8.1) を使用する必要がある場合は、次のいずれかのリンクを参照してください。

- [Windows での AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux での AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy の構成、最適化、トラブルシューティング

「[AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)」を参照してください。

## <a name="next-steps"></a>次の手順

ご質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) ページからお送りください。
