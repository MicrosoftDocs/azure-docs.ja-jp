---
title: 分離されたネットワークでの Azure Disk Encryption
description: この記事では、Linux VM 上での Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 8d8d2b88251f837a23c4e82a90eb4d4eb0043702
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553053"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>分離されたネットワークでの Azure Disk Encryption

ファイアウォール、プロキシ要件、またはネットワーク セキュリティ グループ (NSG) 設定によって接続が制限されていると、必要なタスクを実行するための拡張機能が中断することがあります。 この中断によって、"拡張機能の状態が VM で取得できません" などのステータス メッセージが表示される可能性があります。

## <a name="package-management"></a>パッケージの管理

Azure Disk Encryption は多数のコンポーネントに依存しており、これらは通常は ADE の有効化の一部としてインストールされます (まだ存在しない場合)。 ファイアウォールの内側にあったり、何らかの方法でインターネットから分離されている場合、これらのパッケージは事前にインストールされているか、ローカルで使用可能になっている必要があります。

各ディストリビューションに必要なパッケージを次に示します。 サポートされているディストリビューションとボリュームの種類の完全な一覧については、「[サポートされている VM とオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)」を参照してください。

- **Ubuntu 14.04、16.04、18.04**: lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-six、procps、grub-pc-bin
- **CentOS 7.2 - 7.7**: lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-reencrypt、pyparted、procps-ng、util-linux
- **CentOS 6.8**: lsscsi、psmisc、lvm2、uuid、at、cryptsetup-reencrypt、pyparted、python-six
- **RedHat 7.2 - 7.7**: lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-reencrypt、procps-ng、util-linux
- **RedHat 6.8**: lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup-reencrypt
- **openSUSE 42.3、SLES 12-SP4、12-SP3**: lsscsi、cryptsetup

Red Hat では、プロキシが必要な場合は、サブスクリプション マネージャーと yum が正しく設定されていることを確認する必要があります。 詳細については、「[How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533)」(subscription-manager と yum の問題をトラブルシューティングする方法) を参照してください。  

パッケージを手動でインストールする場合は、新しいバージョンがリリースされたときも手動でアップグレードする必要があります。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
適用されるあらゆるネットワーク セキュリティ グループ設定において、エンドポイントがディスクの暗号化のために規定されているネットワーク構成の前提条件を引き続き満たせるようにする必要があります。  [Azure Disk Encryption:ネットワーク要件](disk-encryption-overview.md#networking-requirements)に関する記事を参照してください。

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD を使用した Azure Disk Encryption (以前のバージョン)

[Azure AD を使用した Azure Disk Encryption (以前のバージョン)](disk-encryption-overview-aad.md) を使用している場合、[Azure Active Directory Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) を ([上記の](#package-management)ディストリビューションに該当するパッケージに加えて) すべてのディストリビューションに手動でインストールする必要があります。

[Azure AD の資格情報](disk-encryption-linux-aad.md)を使用して暗号化を有効にする場合、ターゲット VM は、Azure Active Directory のエンドポイントと Key Vault のエンドポイントの両方への接続を許可する必要があります。 現在の Azure Active Directory 認証エンドポイントは、[Microsoft 365 の URL と IP アドレスの範囲](/microsoft-365/enterprise/urls-and-ip-address-ranges)に関するドキュメントのセクション 56 と 59 に記載されています。 Key Vault の説明は、「[ファイアウォールの向こう側にある Access Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」方法に関するドキュメンテーションにあります。

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

仮想マシンは、その VM 内からしかアクセスできない既知のルーティング不可能な IP アドレス (`169.254.169.254`) を使用する [Azure Instance Metadata サービス](instance-metadata-service.md) エンドポイントにアクセスできる必要があります。  ローカル HTTP トラフィックをこのアドレスに変更する (たとえば X-Forwarded-For ヘッダーを追加する) プロキシ構成はサポートされません。

## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)の追加のステップを参照してください
- [保存時の Azure データの暗号化](../../security/fundamentals/encryption-atrest.md)
