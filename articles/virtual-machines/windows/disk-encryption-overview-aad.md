---
title: Azure AD での Azure Disk Encryption (以前のリリース)
description: この記事では、IaaS VM 用に Microsoft Azure Disk Encryption を使用する場合の前提条件について説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555382"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD での Azure Disk Encryption (以前のリリース)

**Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure AD アプリケーション パラメーターを指定する必要はありません。新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。新しいリリースを使用して VM のディスク暗号化を有効にする手順を表示するには、[Windows VM の Azure Disk Encryption](disk-encryption-overview.md) に関するページを参照してください。Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、AAD 構文を使用して保持し続ける必要があります。**

この記事では、[Windows VM 用の Azure Disk Encryption](disk-encryption-overview.md) の内容を補足すると共に、Azure AD (以前のリリース) を使用した Azure Disk Encryption の追加の要件と前提条件について説明します。 セクション「[サポートされている VM とオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)」は変りありません。

## <a name="networking-and-group-policy"></a>ネットワークとグループ ポリシー

**従来の AAD パラメーター構文を使用して Azure Disk Encryption 機能を有効にするには、IaaS VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。** 
  - Key Vault に接続するためのトークンを取得するには、IaaS VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーを Key Vault に書き込むには、IaaS VM が Key Vault エンドポイントに接続できる必要があります。
  - IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」を参照してください。
  - 暗号化する VM は、既定のプロトコルとして TLS 1.2 を使用するように構成する必要があります。 TLS 1.0 が明示的に無効化されており、.NET バージョンが 4.6 以降に更新されていない場合は、次のレジストリ変更によって ADE を有効にして、より新しい TLS バージョンを選択できるようにします。

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**グループ ポリシー:**
 - Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。 ドメインに参加している VM の場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "互換性のある TPM が装備されていない BitLocker を許可する" のグループ ポリシーについては、「[BitLocker Group Policy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)」(BitLocker グループ ポリシー リファレンス) をご覧ください。

-  ドメインに参加済みであり、カスタム グループ ポリシーを使用する仮想マシンでの BitLocker ポリシーには、次の設定を含める必要があります。[[BitLocker 回復情報のユーザー記憶域を構成する] -> [256 ビットの回復キーを許可する]](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 BitLocker のカスタム グループ ポリシー設定に互換性がない場合、Azure Disk Encryption は失敗します。 正しいポリシー設定がないマシンでは、新しいポリシーを適用し、新しいポリシーを強制的に更新して (gpupdate.exe /force)、再起動する処理が必要になる可能性があります。  

## <a name="encryption-key-storage-requirements"></a>暗号化キーのストレージ要件  

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 ご利用のキー コンテナーと VM は、同じ Azure リージョンおよびサブスクリプションに存在している必要があります。

詳細については、「[Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)」を参照してください。
 
## <a name="next-steps"></a>次のステップ

- [Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)
- [Windows VM で Azure AD を使用して Azure Disk Encryption を有効にする (以前のリリース)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
