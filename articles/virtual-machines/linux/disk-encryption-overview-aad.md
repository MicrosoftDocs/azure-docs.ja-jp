---
title: Azure Disk Encryption と Azure AD アプリの前提条件 (以前のリリース)
description: この記事では、Linux VM 用の Azure Disk Encryption の補足情報と、Azure AD を使用した Azure Disk Encryption の追加の要件と前提条件について説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558340"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Active Directory (AD) での Azure Disk Encryption (以前のリリース)

Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure Active Directory (Azure AD) アプリケーション パラメーターを指定する必要はありません。 新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。 すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。 新しいリリースを使用して VM のディスク暗号化を有効にする手順については、[Linux VM の Azure Disk Encryption](disk-encryption-overview.md) に関するページを参照してください。 Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、AAD 構文を使用して保持し続ける必要があります。

この記事では、[Linux VM 用の Azure Disk Encryption](disk-encryption-overview.md) の補足情報と、Azure AD (以前のリリース) を使用した Azure Disk Encryption の追加の要件と前提条件について説明します。

これらのセクションの情報は変わりません。

- [サポートされている VM とオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [追加の VM 要件](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>ネットワークとグループ ポリシー

従来の AAD パラメーター構文を使用して Azure Disk Encryption 機能を有効にするには、サービスとしてのインフラストラクチャ (IaaS) VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。 
  - Key Vault に接続するためのトークンを取得するには、IaaS VM が Azure AD エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーを Key Vault に書き込むには、IaaS VM が Key Vault エンドポイントに接続できる必要があります。
  - IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」を参照してください。
  - Windows 上で、TLS 1.0 が明示的に無効化され、.NET バージョンが 4.6 以降に更新されていない場合は、次のレジストリ変更によって Azure Disk Encryption を有効にして、より新しい TLS バージョンを選択できるようにします。

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>グループ ポリシー
 - Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。 ドメインに参加している VM の場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "**互換性のある TPM が装備されていない BitLocker を許可する**" オプションのグループ ポリシーについては、「[BitLocker Group Policy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)」(BitLocker グループ ポリシー リファレンス) をご覧ください。

- ドメインに参加済みであり、カスタム グループ ポリシーを使用する仮想マシンでの BitLocker ポリシーには、次の設定を含める必要があります。[[BitLocker 回復情報のユーザー記憶域を構成する] -> [256 ビットの回復キーを許可する]](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 BitLocker のカスタム グループ ポリシー設定に互換性がない場合、Azure Disk Encryption は失敗します。 正しいポリシー設定がないマシンでは、新しいポリシーを適用し、新しいポリシーを強制的に更新して (gpupdate.exe /force)、必要な場合は再起動します。 

## <a name="encryption-key-storage-requirements"></a>暗号化キーのストレージ要件 

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 ご利用のキー コンテナーと VM は、同じ Azure リージョンおよびサブスクリプションに存在している必要があります。

詳細については、「[Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)」を参照してください。
 
## <a name="next-steps"></a>次のステップ

- [Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)
- [Linux VM で Azure AD を使用して Azure Disk Encryption を有効にする (以前のリリース)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
