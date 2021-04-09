---
title: azcopy login | Microsoft Docs
description: この記事では、azcopy login コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878360"
---
# <a name="azcopy-login"></a>azcopy login

Azure Storage リソースにアクセスするために Azure Active Directory にログインします。

## <a name="synopsis"></a>概要

Azure Storage リソースにアクセスするために Azure Active Directory にログインします。

Azure Storage アカウントに対する承認を得るには、お使いのユーザー アカウントに、ストレージ アカウント、親リソース グループ、または親サブスクリプションのいずれかのコンテキストで **ストレージ BLOB データ共同作成者** ロールを割り当てる必要があります。

このコマンドは、OS の組み込みメカニズムを使用して、現在のユーザーの暗号化されたログイン情報をキャッシュします。

> [!IMPORTANT]
> コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="examples"></a>例

共通に設定された既定の AAD テナント ID を使用して対話形式でログインします。

```azcopy
azcopy login
```

指定されたテナント ID を使用して対話形式でログインします。

```azcopy
azcopy login --tenant-id "[TenantID]"
```

仮想マシン (VM) の システム割り当て ID を使用してログインします。

```azcopy
azcopy login --identity
```

VM のユーザー割り当て ID と、サービス ID のクライアント ID を使用してログインします。
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
VM のユーザー割り当て ID と、サービス ID のオブジェクト ID を使用してログインします。

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

VM のユーザー割り当て ID と、サービス ID のリソース ID を使用してログインします。
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

クライアント シークレットを使用して、サービス プリンシパルとしてログインします。環境変数 AZCOPY_SPA_CLIENT_SECRET を、シークレット ベースのサービス プリンシパル認証用のクライアント シークレットに設定します。

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

証明書とそのパスワードを使用して、サービス プリンシパルとしてログインします。

環境変数 AZCOPY_SPA_CERT_PASSWORD を、証明書ベースのサービス プリンシパル認証用の証明書のパスワードに設定します。

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

`/path/to/my/cert` は、PEM または PKCS12 ファイルへのパスとして扱います。 AzCopy は、証明書を取得するためにシステムの証明書ストアにアクセスしません。

証明書ベースのサービス プリンシパル認証を行う場合は、`--certificate-path` が必須です。

## <a name="options"></a>Options

**--aad-endpoint** string    使用する Azure Active Directory エンドポイント。 既定値 (https://login.microsoftonline.com) は、グローバル Azure クラウドに適しています。 各国のクラウドで認証するときに、このパラメーターを設定します。 マネージド サービス ID には必要ありません。

**--application-id** string  ユーザー割り当て ID のアプリケーション ID。 サービス プリンシパル認証に必要です。

**--certificate-path** string  SPN 認証用の証明書へのパス。 証明書ベースのサービス プリンシパル認証に必要です。

**--help**   `azcopy login` コマンドのヘルプ。

**--identity**   マネージド サービス ID (MSI) とも呼ばれる、仮想マシンの ID を使用してログインします。

**--identity-client-id** string  ユーザー割り当て ID のクライアント ID。

**--identity-object-id** string  ユーザー割り当て ID のオブジェクト ID。

**--identity-resource-id** string  ユーザー割り当て ID のリソース ID。

**--service-principal**   証明書またはシークレットを使用して、サービス プリンシパル名 (SPN) を介してログインします。 クライアント シークレットまたは証明書のパスワードは、適切な環境変数に格納する必要があります。 「AzCopy env」と入力すると、環境変数の名前と説明が表示されます。

**--tenant-id** string   OAuth デバイス対話型ログインに使用する Azure Active Directory テナント ID。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   |Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
