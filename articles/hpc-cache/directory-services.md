---
title: Azure HPC Cache で拡張グループを使用する
description: Azure HPC Cache でストレージ ターゲットへのクライアント アクセス用にディレクトリ サービスを構成する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563377"
---
# <a name="configure-directory-services"></a>ディレクトリ サービスを構成する

**ディレクトリ サービス** の設定を使用すると、Azure HPC Cache で外部ソースを使用して、バックエンド ストレージにアクセスするためのユーザーの認証を行うことができます。

ワークフローに 16 個以上のグループのメンバーである NFS ストレージ ターゲットとクライアントが含まれている場合は、**拡張グループ** を有効にする必要がある場合があります。

ボタンをクリックして拡張グループを有効にした後、ユーザーとグループの資格情報を取得するために Azure HPC Cache によって使用されるソースを選択する必要があります。

* [Active Directory](#configure-active-directory) - 外部の Active Directory サーバーから資格情報を取得します。 このタスクに Azure Active Directory を使用することはできません。
* [フラット ファイル](#configure-file-download) - `/etc/group` および `/etc/passwd` ファイルをネットワーク上の場所からダウンロードします。
* [LDAP](#configure-ldap) - Lightweight Directory Access Protocol (LDAP) と互換性のあるソースから資格情報を取得します。

> [!NOTE]
> キャッシュでセキュリティ保護されたサブネットワーク内からそのグループ情報ソースにアクセスできることを確認します。<!-- + details/examples -->

**[Username downloaded]\(ユーザー名ダウンロード済み\)** フィールドには、最新のグループ情報のダウンロードの状態が表示されます。

![拡張グループに対して [はい] オプションが選択され、[Download source]\(ソースをダウンロードする\) というドロップダウン メニューが開かれている、ポータルのディレクトリ サービス ページ設定ページのスクリーンショット](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Active Directory を構成する

このセクションでは、外部の Active Directory (AD) サーバーからユーザーとグループの資格情報を取得するようにキャッシュを設定する方法について説明します。

**[Active Directory の詳細]** で、次の値を指定します。

* **[プライマリ DNS]** - AD ドメイン名を解決するためにキャッシュで使用できるドメイン ネーム サーバーの IP アドレスを指定します。

* **[セカンダリ DNS]** (省略可能) - プライマリ サーバーが使用できない場合に使用するネーム サーバーのアドレスを入力します。

* **[AD DNS ドメイン名]** - 資格情報を取得するためにキャッシュが参加する AD サーバーの完全修飾ドメイン名を指定します。

* **[Cache server name (computer account)]\(キャッシュ サーバー名 (コンピューター アカウント)\)** - AD ドメインに参加するときに、この HPC キャッシュに割り当てる名前を設定します。 このキャッシュとして認識しやすい名前を指定します。 名前の長さは最大 15 文字で、大文字、小文字、数字、ハイフン (-) を使用できます。

**[資格情報]** セクションで、AD サーバーにアクセスするために Azure HPC Cache で使用できる AD 管理者のユーザー名とパスワードを指定します。 この情報は、保存時には暗号化され、クエリを行うことはできません。

ページの上部にあるボタンをクリックして、設定を保存します。

![Active Directory の値が入力されている [ダウンロードの詳細] セクションのスクリーンショット](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>ファイルのダウンロードを構成する

これらの値は、ユーザーとグループの情報が含まれるファイルをダウンロードする場合に必要です。 ファイルは、Linux/UNIX の標準の `/etc/group` および `/etc/passwrd` 形式である必要があります。

* **[User file URI]\(ユーザー ファイルの URI\)** - `/etc/passwrd` ファイルの完全な URI を入力します。
* **[Group file URI]\(グループ ファイルの URI\)** - `/etc/group` ファイルの完全な URI を入力します。

![フラット ファイルのダウンロードに関する [ダウンロードの詳細] セクションのスクリーンショット](media/group-download-details-file.png)

## <a name="configure-ldap"></a>LDAP を構成する

AD 以外の LDAP ソースを使用してユーザーとグループの資格情報を取得する場合は、これらの値を入力します。 これらの値についてのヘルプが必要な場合は、LDAP 管理者に確認してください。

* **[LDAP サーバー]** - 使用する LDAP サーバーの完全修飾ドメイン名または IP アドレスを入力します。 <!-- only one, not up to 3 -->

* **[LDAP base DN]\(LDAP ベース DN\)** - LDAP ドメインのベース識別名を DN 形式で指定します。 ベース DN がわからない場合は、LDAP 管理者に問い合わせてください。

LDAP の動作に必要な設定はサーバーとベース DN だけですが、追加のオプションによって接続のセキュリティが強化されます。

![ディレクトリ サービス ページ設定ページの LDAP 構成領域のスクリーンショット](media/group-download-details-ldap.png)

**[Secure access]\(セキュリティで保護されたアクセス\)** セクションでは、LDAP 接続の暗号化と証明書の検証を有効にすることができます。 **[はい]** をクリックして暗号化を有効にした後は、次のオプションがあります。

* **[証明書の検証]** - これを設定すると、LDAP サーバーの証明書は、下の URI フィールドの証明機関で検証されます。

* **[CA certificate URI]\(CA 証明書の URI\)** - 権限のある証明書へのパスを指定します。 これには、CA で検証された証明書または自己署名証明書へのリンクを指定できます。 外部で検証された証明書の設定を使用するには、このフィールドが必要です。

* **[Auto-download certificate]\(証明書の自動ダウンロード\)** - これらの設定を送信したらすぐに証明書のダウンロードを試みる場合は、 **[はい]** を選択します。

LDAP セキュリティに静的な資格情報を使用する場合は、 **[資格情報]** セクションに入力します。 この情報は、保存時には暗号化され、クエリを行うことはできません。

* **[バインド DN]** - LDAP サーバーへの認証に使用するバインド識別名を入力します。 (DN 形式を使用します。)
* **[バインド パスワード]** - バインド DN のパスワードを指定します。

## <a name="next-steps"></a>次のステップ

* 「[Azure HPC Cache をマウントする](hpc-cache-mount.md)」で、クライアント アクセスに関する詳細を確認します
* 資格情報が正しくダウンロードされない場合は、資格情報のソースについて管理者に問い合わせてください。 必要な場合は、[サポート チケット](hpc-cache-support-ticket.md)を開きます。
