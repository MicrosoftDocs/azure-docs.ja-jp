---
title: 共有リソースを使用してラボを作成する方法 | Azure Lab Services
description: 学生間で共有されるリソースを必要とするラボを作成する方法について説明します。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647972"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Azure Lab Services で共有リソースを使用してラボを作成する方法

クラスルーム ラボを作成するとき、一部のリソースをラボ内のすべての学生間で共有することが必要な場合があります。  たとえば、データベース クラス用のライセンス サーバーや SQL Server などです。  この記事では、ラボ用に共有リソースを有効にする手順について説明します。  また、その共有リソースへのアクセスを制限する方法についても説明します。

## <a name="architecture"></a>アーキテクチャ

次の図に示すように、ラボにはラボ アカウントがあります。  ラボ アカウントには、VNET ピアリングの設定があります。これにより、ラボの仮想ネットワークは共有リソースのネットワークに接続されます。  次の図では、IP 範囲が重複していない 2 つの仮想ネットワークが示されています。  これらの IP 範囲は単なる例です。  また、共有リソースの仮想ネットワークがラボ アカウントと同じサブスクリプションにあることも注意してください。

![Lab Services と共有リソース アーキテクチャ](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>共有リソースをセットアップする

共有リソース用の仮想ネットワークは、ラボを作成する前に作成する必要があります。  仮想ネットワークの作成方法の詳細については、[仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関する記事を参照してください。  ラボのコンピューターの IP アドレスと重複しないように、仮想ネットワークの範囲を計画することが重要です。  ネットワークの計画の詳細については、「[仮想ネットワークを計画する](../virtual-network/virtual-network-vnet-plan-design-arm.md)」の記事を参照してください。 この例の共有リソースは、10.2.0.0/16 の範囲の仮想ネットワーク内にあります。  まだ行っていない場合は、共有リソースを保持するための[サブネットを作成](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)します。  この例では、10.2.0.0/24 の範囲を使用しますが、ネットワークのニーズによって範囲は異なる場合があります。

共有リソースとしては、仮想マシン上で実行されているソフトウェアや、Azure によって提供されるサービスなどがあります。 共有リソースは、プライベート IP アドレスを通して使用できる必要があります。  共有リソースをプライベート IP のみで使用できるようにすると、その共有リソースへのアクセスが制限されます。

図では、学生の VM からのトラフィックを制限するために使用できるネットワーク セキュリティ グループ (NSG) も示されています。  たとえば、学生の VM の IP アドレスからのトラフィックは 1 つの共有リソースにしかアクセスできないことを示すセキュリティ規則を作成できます。  セキュリティ規則の設定方法の詳細については、[ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md#work-with-security-rules)に関する記事を参照してください。 共有リソースへのアクセスを特定のラボに制限したい場合は、[ラボ アカウントのラボ設定](manage-labs.md#view-labs-in-a-lab-account)からラボの IP アドレスを取得し、その IP アドレスからのアクセスのみを許可する受信規則を設定します。  その IP アドレスに対してポート 49152 から 65535 を許可することを忘れないでください。  必要に応じて、[[仮想マシン プール] ページ](how-to-set-virtual-machine-passwords.md)を使用して、学生の VM のプライベート IP アドレスを見つけることができます。

共有リソースが、必要なソフトウェアを実行している Azure 仮想マシンである場合は、仮想マシンに対する既定のファイアウォール規則を変更することが必要になる場合があります。

### <a name="tips-for-shared-resources---license-server"></a>共有リソースのヒント - ライセンス サーバー
一般的な共有リソースの 1 つにライセンス サーバーがあります。ここでは設定に成功する方法に関するいくつかのヒントを示します。
#### <a name="server-region"></a>サーバー リージョン
ライセンス サーバーは、ラボとピアリングされた仮想ネットワークに接続する必要があるため、ラボ アカウントと同じリージョンに配置する必要があります。

#### <a name="static-private-ip-and-mac-address"></a>静的プライベート IP および MAC アドレス
既定では、仮想マシンは動的プライベート IP を持つため、[ソフトウェアを設定する前に、プライベート IP を静的に設定します](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 これにより、プライベート IP と MAC アドレスが静的に設定されます。  

#### <a name="control-access"></a>アクセスを制御する
ライセンス サーバーへのアクセスを制御することは重要です。  VM をセットアップした後も、メンテナンス、トラブルシューティング、および更新のためにアクセスが必要になります。  これを行うには、いくつかの方法があります。
- [Azure Security Center 内で Just-In-Time (JIT) アクセスを設定する。](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [アクセスを制限するためのネットワーク セキュリティ グループを設定する。](../virtual-network/network-security-groups-overview.md)
- [ライセンス サーバーへの安全なアクセスを許可する Bastion をセットアップする。](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>ラボ アカウント

共有リソースを使用するには、[ピアリングされた仮想ネットワーク](how-to-connect-peer-virtual-network.md)を使用するようにラボ アカウントを設定する必要があります。  この場合は、共有リソースが保持されている仮想ネットワークにピアリングします。

>[!WARNING]
>クラス用のラボは、ラボ アカウントを共有リソースの仮想ネットワークにピアリングした **後で**、作成する必要があります。  
テンプレート マシン

ラボ アカウントが仮想ネットワークとピアリングされると、テンプレート マシンは共有リソースにアクセスできるようになります。  アクセスされる共有リソースによっては、ファイアウォール規則の更新が必要になる場合があります。