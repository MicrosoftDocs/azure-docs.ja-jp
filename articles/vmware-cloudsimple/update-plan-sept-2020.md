---
title: Azure VMware Solution by CloudSimple (2020 年 9 月の更新プログラム)
description: この記事では、このメンテナンス操作中に予想されることと、プライベート クラウドへの変更内容について説明します。
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89447688"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple (2020 年 9 月の更新プログラム)

Azure VMware Solution サービスへの重要な更新が 9 月に実行されます。 メンテナンスの一環として送信される電子メール通知には、メンテナンスのタイムラインが記載されています。 この記事では、このメンテナンス操作中に予想されることと、プライベート クラウドへの変更内容について説明します。

> [!NOTE]
> これは中断を伴わないアップグレードです。 アップグレード中に、冗長コンポーネントのいずれかが停止する可能性があります。

## <a name="vmware-infrastructure-upgrade"></a>VMware インフラストラクチャのアップグレード

プライベート クラウドの VMware インフラストラクチャは、新しいバージョンに更新されます。 これには、プライベート クラウドの vCenter、ESXi、NSX-T、およびハイブリッド クラウド拡張 (HCX) コンポーネント (展開されている場合) への更新プログラムが含まれます。

アップグレード中、アップグレード操作のためにノードがメンテナンス モードになる前に、新しいノードがプライベート クラウドに追加されます。 これにより、アップグレード処理中にプライベート クラウドの容量と可用性が維持されます。 VMware コンポーネントのアップグレード中に、vCenter の Web UI にアラームが表示されることがあります。 このアラームは、サービス運用チームによって実行されるメンテナンス操作の一部です。

**コンポーネントのバージョン**

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>データセンターの更新

この更新には、データセンターのインフラストラクチャに対する更新プログラムが含まれています。 中断を伴わない更新プログラムは、メンテナンス期間中に実行されます。 更新処理中は冗長性が低下します。 プライベート クラウドの VMware インフラストラクチャ、ExpressRoute 回線、GlobalReach 接続、およびリンク機能のいずれかに関連するすべてのサイト間 VPN デバイスで、アラートが生成される場合があります。 更新中は、更新の一環としてコンポーネントが再起動されるため、これは正常な動作です。

-   サイト間 VPN が単一インスタンス (非 HA) としてデプロイされている場合は、VPN 接続の再確立が必要になる場合があります。

-   ポイント対サイト VPN 接続を使用している場合は、VPN 接続を再確立する必要があります。

## <a name="post-update"></a>更新後

更新が完了すると、新しいバージョンの VMware コンポーネントが表示されます。 問題が発生した場合、または質問がある場合は、[サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いて、サポート チームにお問い合わせください。
