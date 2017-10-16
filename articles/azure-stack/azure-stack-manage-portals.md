---
title: "Azure Stack での管理者ポータルの使用 | Microsoft Docs"
description: "Azure Stack オペレーターが管理者ポータルを使用する方法を学習します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Azure Stack の管理者ポータルの使用

*適用対象: Azure Stack 統合システムおよび Azure Stack 開発キット*

Azure Stack には、管理者ポータルとユーザー ポータル (*テナント* ポータルとも呼ばれることもあります) の 2 つのポータルがあります。 Azure Stack オペレーターは、管理者ポータルを使用して Azure Stack の日常的な管理と操作を行うことができます。 

## <a name="access-the-administrator-portal"></a>管理者ポータルへのアクセス

開発キット環境の場合は、最初に、リモート デスクトップ接続または仮想プライベート ネットワーク (VPN) を使用して[開発キットのホストに接続](azure-stack-connect-azure-stack.md)できることを確認する必要があります。

管理者ポータルにアクセスするには、ポータルの URL を参照し、Azure Stack オペレーターの資格情報を使用してサインインします。 統合システムの場合は、ポータルの URL は Azure Stack のデプロイのリージョン名と外部 FQDN (完全修飾ドメイン名) によって異なります。

| 環境 | 管理者ポータルの URL |   
| -- | -- | 
| 開発キット| https://adminportal.local.azurestack.external  |
| 統合システム | https://adminportal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![管理者ポータル](media/azure-stack-manage-portals/image1.png)

管理者ポータルでは、次のような操作を行えます。

* インフラストラクチャ (システム正常性、更新プログラム、容量など) の管理
* マーケットプレースの設定
* プランの作成
* ユーザーのサブスクリプションの作成

**[Quickstart tutorial]\(クイック スタート チュートリアル\)**タイルに、最も一般的なタスクのオンライン ドキュメントへのリンクがあります。
 
管理ポータルにはオペレーターが仮想マシン、仮想ネットワーク、ストレージ アカウントなどのリソースを作成するための機能がありますが、リソースを作成してテストするためには、[ユーザー ポータルにサインイン](user/azure-stack-use-portal.md)する必要があります。 (管理ポータルの [Quickstart tutorial]\(クイック スタート チュートリアル\) タイルにある **[仮想マシンの作成]** リンクから仮想マシンを作成できますが、この手順は初期デプロイ後に Azure Stack を検証するときにのみ使用します。)

## <a name="subscription-behavior"></a>サブスクリプションの操作
 
管理者ポータルで利用できるサブスクリプションは 1 つだけです。 そのサブスクリプションは*既定プロバイダー サブスクリプション*です。 管理者ポータルで使用するために他のサブスクリプションを追加することはできません。

Azure Stack オペレーターは、管理者ポータルからユーザー (自分自身を含む) のサブスクリプションを追加できます。 ユーザー (自分自身を含む) は、ユーザー ポータルからこれらのサブスクリプションにアクセスして使用できます。 ユーザー ポータルでは、管理者ポータルの管理機能または運用機能のいずれにもアクセスできません。

管理者ポータルとユーザー ポータルは、Azure Resource Manager の個別のインスタンスでサポートされます。 Resource Manager が別々になっているため、ポータルをまたいでサブスクリプションを使用することはできません。 たとえば、Azure Stack オペレーターとしてユーザー ポータルにサインインした場合は、既定プロバイダー サブスクリプションにアクセスすることはできません。 そのため、どの管理機能にもアクセスできません。 パブリック プランから自分のサブスクリプションを作成することはできますが、テナント ユーザーと見なされます。

  >[!NOTE]
  開発キット環境では、ユーザーが Azure Stack オペレーターと同じテナント ディレクトリに属している場合、ユーザーは管理者ポータルへのサインインをブロックされません。 ただし、どの管理機能にもアクセスできません。 また、管理者ポータルからは、サブスクリプションを追加することも、ユーザー ポータルで利用できるプランにアクセスすることもできません。

## <a name="administrator-portal-tips"></a>管理者ポータルに関するヒント

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ

ダッシュボードには、既定のタイルのセットが含まれています。 **[ダッシュボードの編集]** をクリックして既定のダッシュボードを変更したり、**[新しいダッシュボード]** をクリックしてカスタムのダッシュボードを追加したりできます。 ダッシュボードにはタイルを簡単に追加できます。 たとえば、**[新規]** をクリックし、**[プラン]** を右クリックし、**[ダッシュボードにピン留めする]** をクリックします。

### <a name="quick-access-to-online-documentation"></a>オンライン ドキュメントへのクイック アクセス

Azure Stack オペレーター向けドキュメントにアクセスするには、管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) をクリックし、**[ヘルプとサポート]** をクリックします。

### <a name="quick-access-to-help-and-support"></a>ヘルプとサポートへのクイック アクセス

管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) をクリックし、**[新しいサポート要求]** をクリックすると、次のいずれかが行われます。

- 統合システムを使用している場合は、Microsoft カスタマー サポート サービス (CSS) のサポート チケットを直接開くことができるサイトが開きます。 Microsoft サポートと OEM (Original Equipment Manufacturer) ハードウェア ベンダーのサポートのどちらに連絡するかについては、「[Azure Stack の管理の基本](azure-stack-manage-basics.md)」の「サポートが受けられる場所」セクションをご覧ください。
- 開発キットを使用している場合は、Azure Stack フォーラムのサイトが直接開きます。 これらのフォーラムは定期的にチェックされています。 開発キットは評価環境であるため、Microsoft CSS を通した正式なサポートは提供されていません。

## <a name="next-steps"></a>次のステップ

- [Azure Stack でのリージョンの管理](azure-stack-region-management.md)
