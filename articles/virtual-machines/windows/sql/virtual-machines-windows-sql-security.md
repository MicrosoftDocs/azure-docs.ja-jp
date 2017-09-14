---
title: "Azure における SQL Server のセキュリティに関する考慮事項 | Microsoft Docs"
description: "このトピックでは、Azure Virtual Machine で実行されている SQL Server をセキュリティで保護するための一般的なガイダンスを示します。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2017
ms.author: jroth
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 4ad9156e481eac0bae32bca35a2b126363e5d8b6
ms.contentlocale: ja-jp
ms.lasthandoff: 06/07/2017


---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項

このトピックでは、Azure Virtual Machine (VM) の SQL Server インスタンスへのセキュリティで保護されたアクセスの確立に役立つ全体的なセキュリティ ガイドラインについて説明します。

Azure はいくつかの業界規制および標準に準拠しているため、ユーザーは仮想マシンで実行されている SQL Server を使用して、標準に準拠したソリューションを構築できます。 Azure での法規制遵守に関する情報については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)を参照してください。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>SQL VM へのアクセスの制御

SQL Server 仮想マシンを作成するときに、マシンと SQL Server へのアクセス権を持つユーザーを注意深く制御する方法を検討します。 一般に、次の操作を行う必要があります。

- SQL Server へのアクセスをアクセスが必要なアプリケーションとクライアントのみに制限します。
- ユーザー アカウントとパスワードを管理するためのベスト プラクティスに従います。

次のセクションは、これらの点を考える上での提案を示しています。

## <a name="secure-connections"></a>セキュリティで保護された接続

ギャラリー イメージを使って SQL Server 仮想マシンを作成するときに、[**SQL Server Connectivity**] オプションで、[**ローカル (VM 内のみ)**]、[**プライベート (Virtual Network 内)**]、または [**パブリック (インターネット)**] を選択できます。

![SQL Server Connectivity](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

最善のセキュリティでは、自分のシナリオに最も制限の厳しいオプションを選択します。 たとえば、同じ VM の SQL Server にアクセスするアプリケーションを実行している場合、最もセキュリティで保護された選択は [**ローカル**] です。 SQL Server へのアクセスを必要とする Azure アプリケーションを実行している場合、[**プライベート**] は指定した [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 内でのみの SQL Server への通信をセキュリティで保護します。 SQL Server VM にアクセスする [**パブリック (インターネット)**] が必要な場合、危険を回避するために、このトピックの他のベスト プラクティスに従ってください。

ポータルで選択されたオプションは、VM の[ネットワーク セキュリティ グループ](../../../virtual-network/virtual-networks-nsg.md) (NSG) で受信セキュリティ ルールを使用して、仮想マシンへのネットワーク トラフィックを許可または拒否します。 SQL Server ポート (既定値 1433) へのトラフィックを許可するには、新しい受信 NSG ルールを変更または作成できます。 また、このポートでの通信を許可する、特定の IP アドレスを指定することもできます。

![ネットワーク セキュリティ グループ ルール](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

ネットワーク トラフィックを制限する NSG ルールに加え、仮想マシンで Windows ファイアウォールを使用することもできます。

クラシック デプロイメント モデルでエンドポイントを使用している場合、使用しない仮想マシンのエンドポイントは削除します。 エンドポイントで ACL を使用する手順については、「 [エンドポイントの ACL の管理](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)」を参照してください。 これは、Resource Manager を使用する VM には必要ありません。

最後に、Azure Virtual Machine の SQL Server データベース エンジンのインスタンスで、暗号化された接続オプションを有効にすることを検討してください。 署名付き証明書で SQL Server インスタンスを構成します。 詳細については、「[データベース エンジンへの暗号化接続の有効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)」および「[接続文字列の構文](https://msdn.microsoft.com/library/ms254500.aspx)」をご覧ください。

## <a name="use-a-non-default-port"></a>既定以外のポートの使用

既定では、SQL Server は既知のポート (1433) をリッスンします。 セキュリティ強化のために、既定以外のポート (1401 など) をリッスンするように、SQL Server を構成します。 Azure Portal で SQL Server のギャラリー イメージをプロビジョニングする場合、[**SQL Server 設定**] ブレードでこのポートを指定できます。

プロビジョニングした後に、これを構成するには、次の 2 つのオプションがあります。

- Resource Manager VM の場合、VM の概要ブレードから [**SQL Server の構成**] を選択できます。 ここには、ポートを変更するオプションがあります。

  ![ポータルの TCP ポートの変更](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- ポータルでプロビジョニングされていないクラシック VM または SQL Server VM の場合、VM にリモートで接続して、ポートを手動で構成できます。 構成手順については、「[Configure a Server to Listen on a Specific TCP Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)」 (特定の TCP ポートをリッスンするようにサーバーを構成する) を参照してください。 この手動の方法を使用する場合は、その TCP ポートで受信トラフィックを許可するために、Windows ファイアウォール ルールを追加する必要もあります。

> [!IMPORTANT]
> SQL Server ポートがパブリック インターネットの接続を開いている場合、既定以外のポートを指定することをお勧めします。

SQL Server が既定以外のポートをリッスンしている場合は、接続時のポートを指定する必要があります。 たとえば、サーバーの IP アドレスが 13.55.255.255 で、SQL Server がポート 1401 をリッスンしているシナリオを検討します。 SQL Server に接続するには、接続文字列で `13.55.255.255,1401` を指定します。

## <a name="manage-accounts"></a>[アカウントの管理]

攻撃者に簡単にアカウント名やパスワードを推測されたくありません。 次のヒントを使用すると役立ちます。

- **Administrator**という名前ではない一意のローカル管理者アカウントを作成します。

- すべてのアカウントに複雑で強力なパスワードを使用します。 強力なパスワードを作成する方法の詳細については、「[強力なパスワードを作成する](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)」を参照してください。

- 既定で、Azure は SQL Server 仮想マシンのセットアップ中に Windows 認証を選択します。 そのため、 **SA** ログインは無効となり、パスワードはセットアップによって割り当てられます。 **SA** ログインは使用せず、有効にしないことをお勧めします。 SQL ログインが必要な場合は、次の方法のいずれかを使用します。

  - **sysadmin** メンバーシップを持つ一意の名前で SQL アカウントを作成します。 プロビジョニング中に **SQL 認証**を有効にすると、ポータルからこの操作を行うことができます。

    > [!TIP] 
    > プロビジョニング中に SQL 認証を有効にしない場合は、認証モードを手動で [**SQL Server 認証モードと Windows 認証モード**] に変更する必要があります。 詳細については、「 [サーバーの認証モードの変更](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)」を参照してください。

  - **SA** ログインを使用する必要がある場合は、プロビジョニング後にログインを有効にし、新しい強力なパスワードを割り当てます。

## <a name="follow-on-premises-best-practices"></a>オンプレミスのベスト プラクティスに従う

このトピックで説明している方法に加えて、該当する場合は、従来のオンプレミスのセキュリティの方法を確認して実装することお勧めします。 詳細については、「[SQL Server インストールにおけるセキュリティの考慮事項](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)」を参照してください。

## <a name="next-steps"></a>次のステップ

パフォーマンスに関するベスト プラクティスにも関心がある場合は、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」をご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」をご覧ください。


