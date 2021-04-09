---
title: AD DS に参加している VM に Azure ファイル共有をマウントする
description: オンプレミスの Active Directory Domain Services に参加しているマシンにファイル共有をマウントする方法について学習します。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 1dd2768c7541312b68e2a44a35fc4260d67c5655
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587000"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>パート 4: ドメインに参加している VM からファイル共有をマウントする

この記事を始める前に、前の [SMB 経由でディレクトリおよびファイル レベルのアクセス許可を構成する方法](storage-files-identity-ad-ds-configure-permissions.md)に関する記事を完了していることを確認してください。

この記事で説明するプロセスでは、ファイル共有とアクセス許可が正しく設定されていることと、ドメインに参加している VM から Azure ファイル共有にアクセスできることを確認します。 共有レベルの Azure ロールの割り当ては、有効になるまでに時間がかかる場合があります。 

次の図のように、アクセス許可を付与した資格情報を使用してクライアントにサインインします。

![ユーザー認証のための Azure AD サインイン画面を示すスクリーン ショット](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>前提条件のマウント

ファイル共有をマウントする前に、次の前提条件を完了していることを確認してください。

- 対象のストレージ アカウント キーを使用してファイル共有を以前にマウントしたクライアントからファイル共有をマウントする場合は、その共有を切断したこと、ストレージ アカウント キーの永続的な資格情報を削除したこと、および認証に AD DS 資格情報を現在使用していることを確認してください。 マウントされた共有をストレージ アカウント キーでクリアする手順については、[FAQ ページ](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication)を参照してください。
- クライアントには、お使いの AD DS への通信経路が必要です。 お使いのマシンまたは VM が AD DS によって管理されているネットワークの外にある場合、VPN を有効にして、認証のために AD DS に到達できるようにする必要があります。

プレースホルダーの値を実際の値に置き換えて、次のコマンドを使用して Azure ファイル共有をマウントします。 必ず次に示すパスを使用してマウントする必要があります。 ファイルのマウントで CNAME を使用することは、ID ベース認証 (AD DS または Azure AD DS) ではサポートされていません。

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

AD DS の資格情報を使用したマウントで問題が発生した場合は、「[AD 資格情報を使用して Azure Files をマウントできない](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)」を参照してください。

対象のファイル共有のマウントに成功した場合は、対象の Azure ファイル共有に対してオンプレミスの AD DS 認証が正常に有効化され、構成されています。

## <a name="next-steps"></a>次のステップ

ストレージ アカウントを表すために AD DS で作成した ID が、パスワードのローテーションが適用されるドメインまたは OU 内にある場合は、次の記事に進み、パスワードを更新する手順を確認してください。

[AD DS のストレージ アカウント ID のパスワードを更新します](storage-files-identity-ad-ds-update-password.md)