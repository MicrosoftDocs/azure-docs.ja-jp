---
title: "Azure における SQL Server のセキュリティに関する考慮事項 | Microsoft Docs"
description: "このトピックでは、クラシック デプロイ モデルで作成されたリソースを参照し、Azure 仮想マシンで実行している SQL Server をセキュリティ保護するための一般的なガイダンスを示します。"
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
ms.date: 11/15/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9fc96d70592bd55685ebbf1b80f6017b74f58925
ms.lasthandoff: 03/25/2017


---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項
このトピックでは、Azure VM の SQL Server インスタンスへのセキュリティで保護されたアクセスの確立に役立つ全体的なセキュリティ ガイドラインについて説明します。 ただし、Azure の SQL Server データベース インスタンスをより安全に保護するには、Azure のセキュリティに関するベスト プラクティスに加えて、従来のオンプレミスのセキュリティに関するベスト プラクティスも実装することをお勧めします。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

SQL Server のセキュリティ ベスト プラクティスの詳細については、「 [SQL Server 2008 R2 Security Best Practices - Operational and Administrative Tasks (SQL Server 2008 R2 のセキュリティに関するベスト プラクティス - 運用作業と管理作業)](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure はいくつかの業界規制および標準に準拠しているため、ユーザーは仮想マシンで実行されている SQL Server を使用して、標準に準拠したソリューションを構築できます。 Azure での法規制遵守に関する情報については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)を参照してください。

次に示すのは、Azure VM の SQL Server インスタンスへの接続とその構成のときに考慮することをお勧めする、セキュリティに関する推奨事項の一覧です。

## <a name="considerations-for-managing-accounts"></a>アカウントの管理に関する注意点:
* **Administrator**という名前ではない一意のローカル管理者アカウントを作成します。
* すべてのアカウントに複雑で強力なパスワードを使用します。 強力なパスワードを作成する方法の詳細については、「[強力なパスワードの作成に関するヒント](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password)」をご覧ください。
* 既定で、Azure は SQL Server 仮想マシンのセットアップ中に Windows 認証を選択します。 そのため、 **SA** ログインは無効となり、パスワードはセットアップによって割り当てられます。 **SA** ログインは使用せず、有効にしないことをお勧めします。 次に示すのは、SQL ログインが必要な場合の代替方法です。
  
  * sysadmin メンバーシップを持つ SQL アカウントを作成します。
  * **SA** ログインを使用する必要がある場合は、ログインを有効にし、名前を変更して新しいパスワードを割り当てます。
  * 上記のどちらのオプションでも、認証モードを **[SQL Server 認証モードと Windows 認証モード]**に変更する必要があります。 詳細については、「 [サーバーの認証モードの変更](https://msdn.microsoft.com/library/ms188670.aspx)」を参照してください。

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Azure 仮想マシンへの接続のセキュリティ保護に関する注意事項:
* 仮想マシンの管理には、パブリック RDP ポートではなく、 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) を使用することを検討してください。
* [ネットワーク セキュリティ グループ](../../../virtual-network/virtual-networks-nsg.md) (NSG) を使用して、仮想マシンへのネットワーク トラフィックを許可または拒否します。 エンドポイントの ACL が既に導入されている場合に NSG を使用するには、初めにエンドポイントの ACL を削除します。 これを行う方法については、 [PowerShell を使用したエンドポイントの Access Control リスト (ACL) の管理](../../../virtual-network/virtual-networks-acl-powershell.md)を参照してください。
* エンドポイントを使用している場合、使用しない仮想マシンのエンドポイントは削除します。 エンドポイントで ACL を使用する手順については、「 [エンドポイントの ACL の管理](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)」を参照してください。
* Azure Virtual Machines の SQL Server データベース エンジンのインスタンスで、暗号化された接続オプションを有効にします。 署名付き証明書で SQL Server インスタンスを構成します。 詳細については、「[データベース エンジンへの暗号化接続の有効化](https://msdn.microsoft.com/library/ms191192.aspx)」および「[接続文字列の構文](https://msdn.microsoft.com/library/ms254500.aspx)」をご覧ください。
* 仮想マシンが特定のネットワークからのみアクセスされる場合は、Windows ファイアウォールを使用して、特定の IP アドレスまたはネットワーク サブネットへのアクセスを制限します。

## <a name="next-steps"></a>次のステップ
パフォーマンスに関するベスト プラクティスにも関心がある場合は、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」をご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」をご覧ください。


