---
title: Azure Security Center での Just-In-Time 仮想マシン アクセスについて
description: このドキュメントでは、Azure Security Center での Just-In-Time VM アクセスにより、Azure 仮想マシンへのアクセスを制御しやすくする方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93285727"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Just-In-Time (JIT) VM アクセスについて

このページでは、Azure Security Center の Just-In-Time (JIT) VM アクセス機能の背後にある原則と、推奨事項の背後にあるロジックについて説明します。

Azure portal (Security Center または Azure Virtual Machines) またはプログラムを使用して、VM に JIT を適用する方法については、[JIT を使用して管理ポートをセキュリティで保護する方法](security-center-just-in-time.md)に関する記事を参照してください。


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>仮想マシン上の開いている管理ポートのリスク

脅威アクターは、RDP や SSH などの、開いている管理ポートがあるアクセス可能なマシンを積極的に探します。 すべての仮想マシンは、攻撃の対象となる可能性があります。 VM への侵害が成功すると、これは環境内のリソースをさらに攻撃するためのエントリ ポイントとして使用されます。



## <a name="why-jit-vm-access-is-the-solution"></a>JIT VM アクセスがソリューションである理由 

すべてのサイバーセキュリティ防止技術と同様に、目標は攻撃面を減らすことです。 ここでは、開いているポート (特に管理ポート) を減らすことを意味します。

正当なユーザーもこれらのポートを使用しているため、これらを閉じたままにしておくのは現実的ではありません。

このジレンマを解決するために、Azure Security Center には JIT が用意されています。 JIT により、VM へのインバウンド トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>ネットワーク セキュリティ グループと Azure Firewall での JIT の動作方法

Just-In-Time VM アクセスを有効にすると、受信トラフィックをブロックする VM のポートを選択できます。 Security Center により、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) と [Azure Firewall 規則](../firewall/rule-processing.md)で選択したポートに対して "すべての受信トラフィックを拒否" 規則が存在することが保証されます。 これらの規則により、Azure VM の管理ポートへのアクセスが制限され、攻撃から保護されます。 

選択したポートに対して他の規則がすでに存在している場合は、既存の規則が新しい "すべての受信トラフィックを拒否" 規則よりも優先されます。 選択したポートに既存の規則がない場合は、NSG と Azure Firewall で新しい規則が優先されます。

ユーザーが VM へのアクセス権を要求すると、Security Center によってそのユーザーが VM に対する [Azure ロール ベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) アクセス許可を持っているかどうかがチェックされます。 要求が承認されると、Security Center では、関連する IP アドレス (または範囲) から選択したポートへの受信トラフィックを指定された時間だけ許可するように、NSG および Azure Firewall が構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 既に確立されている接続は中断されません。

> [!NOTE]
> JIT では、[Azure Firewall Manager](../firewall-manager/overview.md) によって制御される Azure Firewall によって保護されている VM はサポートされません。




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>JIT を適用すべき VM を Security Center で特定する方法

次の図は、サポートされている VM の分類を決定する際に Security Center で適用されるロジックを示しています。 

[![Just-In-Time (JIT) 仮想マシン (VM) のロジック フロー](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

JIT の利点を得られるマシンが Security Center で検出されると、そのマシンが推奨事項の **[異常なリソース]** タブに追加されます。 

![Just-In-Time (JIT) 仮想マシン (VM) のアクセスに関する推奨事項](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>FAQ - Just-In-Time 仮想マシンへのアクセスに関する質問

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>JIT を構成して使用するために必要なアクセス許可は何ですか?

JIT では、サブスクリプションで [Azure Defender for servers](defender-for-servers-introduction.md) を有効にする必要があります。 

**閲覧者** と **セキュリティ閲覧者** ロールは、JIT の状態とパラメーターの両方を表示できます。

JIT で動作するカスタム ロールを作成する場合は、次の表にある詳細情報が必要となります。

> [!TIP]
> VM への JIT アクセスを要求する必要があり、他の JIT 操作を実行しないユーザーに対して最小特権のロールを作成するには、Security Center GitHub コミュニティ ページにある [Set-JitLeastPrivilegedRole スクリプト](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)を使用します。

| ユーザーを有効にする目的: | 設定するアクセス許可|
| --- | --- |
|VM の JIT ポリシーを構成または編集する | *これらのアクションをロールに割り当てます。*  <ul><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> VM のサブスクリプションまたはリソース グループの範囲: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|VM への JIT アクセスを要求する | *これらのアクションをユーザーに割り当てます。*  <ul><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  VM のサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  VM のサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT ポリシーの読み取り| *これらのアクションをユーザーに割り当てます。*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>次のステップ

このページでは、Just-In-Time (JIT) 仮想マシン (VM) へのアクセスを使用すべき _理由_ について説明しました。 

JIT を有効にし、JIT 対応の VM へのアクセスを要求する方法については、操作方法に関する記事に進んでください。

> [!div class="nextstepaction"]
> [JIT を使用して管理ポートをセキュリティで保護する方法](security-center-just-in-time.md)