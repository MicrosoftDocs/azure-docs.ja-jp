---
title: Microsoft 365 Defender と Azure Sentinel の統合 |Microsoft Docs
description: Microsoft 365 Defender と Azure Sentinel を統合することにより、M365 のセキュリティ インシデントの調査を支援する M365D の強みを維持しながら、Azure Sentinel をユニバーサル インシデント キューとして使用できるようにする方法と、Defender コンポーネントの高度なハンティング データを Azure Sentinel に取り込む方法についても説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743964"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender と Azure Sentinel の統合

> [!IMPORTANT]
> 現在、Microsoft 365 Defender コネクタは **プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

> [!IMPORTANT]
>
> **Microsoft 365 Defender** は、以前は **Microsoft Threat Protection** または **MTP** と呼ばれていました。
>
> **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
> 以前の名前がしばらくの間使用されることがあります。

## <a name="incident-integration"></a>インシデント統合

Azure Sentinel の [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) インシデント統合により、すべての M365D インシデントを Azure Sentinel にストリーミングし、両方のポータル間で同期を保つことができます。 M365D (旧称 Microsoft Threat Protection または MTP) のインシデントには、関連するすべてのアラート、エンティティ、関連情報が含まれており、Azure Sentinel でトリアージと事前調査を実行するのに十分なコンテキストが提供されます。 Sentinel に入ると、インシデントは M365D と双方向で同期されたままになるため、インシデント調査で両方のポータルの利点を活用できます。

この統合により、Microsoft 365 セキュリティ インシデントは、組織全体の主要なインシデント キューの一部として、Azure Sentinel 内から管理できるようになります。これにより、M365 インシデントを、他のすべてのクラウドおよびオンプレミスシステムのものと合わせて確認し、関連付けることができます。 同時に、M365D の独自の強みと機能を活用して、詳細な調査や、M365 エコシステム全体での M365 固有のエクスペリエンスを実現できます。 M365 Defender は、複数の M365 製品からのアラートを強化してグループ化します。これにより、SOC のインシデント キューのサイズが縮小され、解決にかかる時間が短縮されます。 M365 Defender スタックの一部であるコンポーネント サービスは次のとおりです。

- **Microsoft Defender for Endpoint** (MDE、旧称 MDATP)
- **Microsoft Defender for Identity** (MDI、旧称 AATP)
- **Microsoft Defender for Office 365** (MDO、旧称 O365ATP)
- **Microsoft Cloud App Security** (MCAS)

これらのコンポーネントからアラートを収集するだけでなく、M365 Defender によって独自のアラートが生成されます。 これらのすべてのアラートからインシデントを作成し、Azure Sentinel に送信します。

### <a name="common-use-cases-and-scenarios"></a>一般的なユース ケースとシナリオ

- 1 回のクリックで、M365 Defender インシデント (M365 Defender コンポーネントのすべてのアラートとエンティティを含む) を Azure Sentinel に接続します。

- 状態、所有者、終了の理由について、Sentinel と M365D インシデント間の双方向同期を行います。

- Azure Sentinel で M365 Defender アラートのグループ化と強化の機能を活用して、解決までの時間を短縮します。

- Azure Sentinel インシデントとその並行 M365 Defender インシデント間のコンテキスト内ディープ リンクにより、両方のポータルでの調査を容易にします。

### <a name="connecting-to-microsoft-365-defender"></a>Microsoft 365 Defender に接続する

Microsoft 365 Defender データ コネクタを有効にして [インシデントとアラートを収集](connect-microsoft-365-defender.md)すると、M365D インシデントは、M365 Defender で生成されるとすぐに、 **[Product name]\(製品名\)** フィールドに **Microsoft 365 Defender** と共に Azure Sentinel インシデント キューに表示されます。
- M365 Defender でインシデントが生成されてから Azure Sentinel に表示されるまで、最大で10 分かかることがあります。

- インシデントは、追加コストなしで取り込まれて同期されます。

M365 Defender 統合が接続されると、すべてのコンポーネント アラート コネクタ (MDE、MDI、MDO、MCAS) は、まだ接続されていなければ、自動的にバックグラウンドで接続されます。 M365 Defender の接続後にいずれかのコンポーネント ライセンスを購入した場合でも、新しい製品のアラートとインシデントは、追加の構成や料金なしで Azure Sentinel に送信されます。

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender インシデントと Microsoft インシデントの作成ルール

- M365 Defender によって生成されるインシデントは、M365 セキュリティ製品からのアラートに基づいて、M365 カスタム M365 ロジックを使用して作成されます。

- Azure Sentinel の Microsoft インシデント作成ルールも、(別の) カスタム Azure Sentinel ロジックを使用して、同じアラートからインシデントを作成します。

- 両方のメカニズムを一緒に使用することは完全にサポートされており、この構成を使用して、新しい M365 Defender インシデント作成ロジックへの移行を容易にすることができます。 しかし、これにより、同じアラートに対して **重複したインシデント** が作成されます。

- 同じアラートに対して重複したインシデントが作成されないようにするには、M365 Defender に接続する際に、M365 製品 (MDE、MDI、MDO (下の MCAS を参照)) に対するすべての **Microsoft インシデント作成ルール** をオフにすることをお勧めします。 これを行うには、コネクタ ページで関連するチェック ボックスをマークします。 これを行うと、インシデント作成ルールによって適用されたフィルターが M365 Defender インシデント統合に適用されないことに注意してください。

- Microsoft Cloud App Security (MCAS) アラートの場合、すべてのアラートの種類が現在 M365 Defender にオンボードされているわけではありません。 すべての MCAS アラートのインシデントを確実に取得するには、M365D に "*オンボードされていない*" アラートの種類に対して **Microsoft インシデント作成ルール** を保持または作成する必要があります。

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Azure Sentinel と双方向同期での M365 Defender インシデントの処理

M365 Defender インシデントは、Azure Sentinel インシデント キューに、製品名 **Microsoft 365 Defender** と、他の Sentinel インシデントと同様の詳細と機能を含めて表示されます。 各インシデントには、M365 Defender ポータルの並列インシデントへのリンクが含まれます。

インシデントが M365 Defender で進化し、より多くのアラートまたはエンティティが追加されると、それに応じて Azure Sentinel インシデントが更新されます。

M365D または Azure Sentinel で、M365 インシデントの状態、終了理由、または割り当てに加えられた変更は、他のインシデント キューでも同様に更新されます。 同期は、インシデントに対する変更が適用されるとすぐに、両方のポータルで遅延なく行なわれます。 最新の変更内容を確認するには、最新の情報に更新する必要がある場合があります。

M365 Defender では、1 つのインシデントからのすべてのアラートが別のものに転送され、インシデントがマージされることがあります。 これが起きると、Azure Sentinel のインシデントに変更が反映されます。 一方のインシデントには両方の元のインシデントからの全アラートが含まれ、他方のインシデントは自動的に閉じられ、"リダイレクト済み" のタグが追加されます。

> [!NOTE]
> Azure Sentinel のインシデントには、最大で 150 のアラートが含まれることがあります。 M365D インシデントの場合は、これを超えることがあります。 アラートが 150 を超える M365D インシデントが Azure Sentinel に同期されている場合、Sentinel インシデントは "150 +" のアラートを持つものとして表示され、アラートの完全なセットを確認できる M365D の並列インシデントへのリンクが提供されます。

## <a name="advanced-hunting-event-collection"></a>高度なハンティング イベント収集

Microsoft 365 Defender コネクタを使用すると、**高度なハンティング** イベント (生のイベント データの種類) を、Microsoft 365 Defender およびそのコンポーネント サービスから Azure Sentinel にストリーミングすることもできます。 現在、[Microsoft Defender For Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) の[高度なハンティング](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) イベントを収集して、それらを Azure Sentinel ワークスペースの専用のテーブルに直接ストリーミングすることができます。 これらのテーブルは Microsoft 365 Defender ポータルで使用されているのと同じスキーマに基づいて構築されており、高度なハンティング イベントの完全なセットに完全にアクセスして、次のことを行うことができます。

- 既存の Microsoft Defender for Endpoint の高度なハンティング クエリを Azure Sentinel に簡単にコピーする。

- 生のイベント ログを使用して、アラート、ハンティング、調査に関する追加の分析情報を提供し、これらのイベントを Azure Sentinel の追加データ ソースの他のものと関連付ける。

- Microsoft Defender for Endpoint または Microsoft 365 Defender の 30 日間の既定の保有期間を超えて、延長された保有期間でログを保存する。 これを行うには、ワークスペースの保有期間を構成するか、Log Analytics でテーブルごとの保有期間を構成します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft 365 Defender コネクタを使用して Microsoft 365 Defender を Azure Sentinel と一緒に使用することの利点について学習しました。

- [Microsoft 365 Defender コネクタを有効にする](connect-microsoft-365-defender.md)手順を確認します。
- [カスタム アラート](tutorial-detect-threats-custom.md)を作成して[インシデントを調査](tutorial-investigate-cases.md)します。
