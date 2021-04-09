---
title: Azure SQL Database のセキュリティ機能
description: この記事では、Azure の Azure SQL Database で顧客データを保護する方法についての概要を説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84021859"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database のセキュリティ機能    
Azure SQL Database は、Azure でリレーショナル データベース サービスを提供しています。 顧客データを保護し、お客様がリレーショナル データベース サービスに期待する強力なセキュリティ機能を提供するために、SQL Database には独自のセキュリティ機能があります。 これらの機能は、Azure から継承されたコントロールに基づいて構築されています。

## <a name="security-capabilities"></a>セキュリティ機能

### <a name="usage-of-the-tds-protocol"></a>TDS プロトコルの使用
Azure SQL Database は表形式データ ストリーム (TDS) プロトコルのみをサポートしているため、既定の TCP/1433 のポート経由でのみデータベースにアクセスできます。

### <a name="azure-sql-database-firewall"></a>Azure SQL Database ファイアウォール
顧客データを保護するために、Azure SQL Database にはファイアウォール機能があります。この機能の既定では、以下のように SQL Database へのアクセスをすべて禁止しています。

![Azure SQL Database ファイアウォール](./media/infrastructure-sql/sql-database-firewall.png)

ゲートウェイ ファイアウォールではアドレスを制限できるので、お客様は受け入れ可能な IP アドレスの範囲を細かく指定することができます。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてアクセス権を付与します。

ファイアウォールの構成は、管理ポータルを使用するか、Azure SQL Database Management REST API を使用してプログラムで行うことができます。 Azure SQL Database ゲートウェイ ファイアウォールの既定では、Azure SQL Database に対するすべてのお客様の TDS アクセスが禁止されています。 お客様は、アクセス制御リスト (ACL) を使用して、送信元と宛先のインターネット アドレス、プロトコル、およびポート番号を指定して Azure SQL Database の接続を許可することで、アクセスを構成する必要があります。

### <a name="dosguard"></a>DoSGuard
サービス拒否 (DoS) 攻撃は、DoSGuard という SQL Database ゲートウェイ サービスによって軽減されます。 DoSGuard は、IP アドレスからの失敗したログインを積極的に追跡します。 一定の期間内に特定の IP アドレスから失敗したログインが複数回ある場合、その IP アドレスは、事前に定義された期間、サービス内のすべてのリソースにアクセスできなくなります。

さらに、Azure SQL Database ゲートウェイでは次の処理が実行されます。

- データベース サーバーに接続するときに、TDS FIPS 140-2 で検証済みの暗号化された接続を実装するためのセキュリティで保護されたチャネル機能のネゴシエーション。
- クライアントからの接続を受け入れるときのステートフル TDS パケット インスペクション。 ゲートウェイは接続情報を検証し、接続文字列に指定されたデータベース名に基づいて適切な物理サーバーに TDS パケットを渡します。

Azure SQL Database サービスのネットワーク セキュリティの重要な原則は、サービスが動作するために必要な接続と通信のみを許可することです。 その他のすべてのポート、プロトコル、および接続は、既定でブロックされています。 仮想ローカル エリア ネットワーク (VLAN) と ACL は、送信元と宛先のネットワーク、プロトコル、およびポート番号によるネットワーク通信の制限に使用されます。

ネットワークベースの ACL を実装するための承認済みメカニズムには、ルーターおよびロード バランサーに対する ACL などがあります。 これらのメカニズムは Azure Networking、ゲスト VM ファイアウォール、および Azure SQL Database ゲートウェイ ファイアウォールのルールによって管理され、お客様によって構成されています。

## <a name="data-segregation-and-customer-isolation"></a>データの分離とお客様の分離
Azure 運用環境ネットワークは、パブリックにアクセス可能なシステム コンポーネントが内部リソースから分離されるように構成されています。 一般に公開されている Azure portal へのアクセスを提供する Web サーバーと、顧客アプリケーション インスタンスと顧客データが存在する基礎となる Azure 仮想インフラストラクチャの間には、物理的および論理的な境界が存在します。

パブリックにアクセス可能なすべての情報は、Azure 運用環境ネットワーク内で管理されています。 運用環境ネットワークは、2 要素認証と境界保護メカニズムの対象であり、前のセクションで説明したファイアウォールおよびセキュリティ機能セットを使用し、以下のセクションに示すデータの分離機能を使用しています。

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>未承認のシステムと FC の隔離
ファブリック コントローラー (FC) は Azure Fabric の中心のオーケストレーターなので、脅威 (特に顧客アプリケーション内の侵害されている可能性がある FA からの脅威) を軽減するために、重要なコントロールが用意されています。 FC は、デバイス情報 (MAC アドレスなど) が FC 内に事前に読み込まれていないハードウェアを認識しません。 FC 上の DHCP サーバーは、起動するノードの MAC アドレスの一覧を構成しています。 承認されていないシステムが接続されている場合でも、ファブリック インベントリに組み込まれていないため、ファブリック インベントリ内のシステムとは接続されません (通信は承認されません)。 そのため、承認されていないシステムが FC と通信し、VLAN と Azure にアクセスするリスクが軽減されます。

### <a name="vlan-isolation"></a>VLAN の分離
Azure の運用環境ネットワークは、論理的に 3 つのプライマリ VLAN に分離されています。

- メイン VLAN: 信頼されていない顧客ノードを相互接続します。
- FC VLAN: 信頼されている FC やサポート システムが含まれています。
- デバイス VLAN: 信頼されているネットワークおよびその他のインフラストラクチャ デバイスが含まれています。

### <a name="packet-filtering"></a>パケット フィルタリング
ノードのルート OS とゲスト OS に実装されている IPFilter とソフトウェア ファイアウォールは、VM 間に接続制限を課し、不正なトラフィックを防止します。

### <a name="hypervisor-root-os-and-guest-vms"></a>ハイパーバイザー、ルート OS、ゲスト VM
ルート OS とゲスト VM の分離と、ゲスト VM 間の分離は、ハイパーバイザーとルート OS によって管理されます。

### <a name="types-of-rules-on-firewalls"></a>ファイアウォールに対するルールの種類
ルールは次のように定義されます。

{Src IP, Src Port, Destination IP, Destination Port, Destination Protocol, In/Out, Stateful/Stateless, Stateful Flow Timeout}.

同期アイドル文字 (SYN) パケットは、ルールのいずれかが許可されている場合にのみ、許可または禁止されます。 TCP の場合、Azure はステートレス ルールを使用します。このルールの原則では、SYN 以外のすべてのパケットのみが VM に出入りすることを許可されます。 このセキュリティの前提は、すべてのホスト スタックは、SYN パケットを以前に見たことがない場合、非 SYN の無視から回復できることです。 TCP プロトコル自体はステートフルであり、ステートレスな SYN ベースのルールと組み合わせて、ステートフル実装の全体的な動作を実現します。

ユーザー データグラム プロトコル (UDP) の場合、Azure はステートフル ルールを使用します。 UDP パケットがルールと一致するたびに、逆方向に反転フローが作成されます。 このフローには、組み込みのタイムアウトがあります。

Azure が提供する機能の上に独自のファイアウォールを設定する操作は、お客様が行ってください。 これで、受信および送信トラフィックのルールをお客様が定義できます。

### <a name="production-configuration-management"></a>運用環境の構成管理
Azure および Azure SQL Database の各運用チームは、標準のセキュリティで保護された構成を保守しています。 運用環境システムに対するすべての構成変更は、中央の追跡システムによって文書化され、追跡されます。 ソフトウェアとハードウェアの変更は、中央の追跡システムによって追跡されます。 ACL に関するネットワークの変更は、ACL 管理サービスを使用して追跡されます。

Azure に対するすべての構成変更は、ステージング環境で開発およびテストされ、その後に運用環境に展開されます。 ソフトウェア ビルドは、テストの一部としてレビューされます。 セキュリティとプライバシーのチェックは、エントリ チェックリスト基準の一部としてレビューされます。 変更は、各展開チームによってスケジュールされた間隔で展開されます。 リリースは、運用環境に展開される前に、各展開チームの担当者によって確認され、承認されます。

変更が成功したかどうかが監視されます。 障害シナリオでは、変更は以前の状態にロールバックされます。または、指定された担当者の承認を得て障害に対処する修正プログラムが展開されます。 Source Depot、Git、TFS、マスター データ サービス (MDS)、Runners、Azure セキュリティ監視、FC、および WinFabric プラットフォームは、Azure 仮想環境の構成設定を一元的に管理、適用、検証するために使用されます。

同様に、ハードウェアとネットワークの変更によって、ビルド要件への準拠性を評価する検証手順が確立されています。 これらのリリースは、スタック全体の各グループの連携された変更諮問委員会 (CAB) を介してレビューおよび承認されます。

## <a name="next-steps"></a>次のステップ
Microsoft が提供する Azure アーキテクチャの保護の詳細については、以下を参照してください。

- [Azure の施設、建物、および物理上のセキュリティ](physical-security.md)
- [Azure インフラストラクチャの可用性](infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](infrastructure-network.md)
- [Azure 実稼働環境のネットワーク](production-network.md)
- [Azure の実稼働環境の運用と管理](infrastructure-operations.md)
- [Azure インフラストラクチャの監視](infrastructure-monitoring.md)
- [Azure インフラストラクチャの整合性](infrastructure-integrity.md)
- [Azure での顧客データの保護](protection-customer-data.md)
