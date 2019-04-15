---
title: Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs
description: Azure Stack Development Kit の機能強化、修正、既知の問題。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915885"
---
# <a name="asdk-release-notes"></a>ASDK リリース ノート

この記事では、Azure Stack Development Kit (ASDK) の変更、修正、および既知の問題に関する情報を提供します。 実行しているバージョンが不明な場合は、[ポータルを使用して確認](../azure-stack-updates.md#determine-the-current-version)できます。

[![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [フィード](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)をサブスクライブして、ASDK の新着情報を常に把握するようにしてください。

## <a name="build-1903"></a>ビルド 1903

1903 ペイロードに、ASDK リリースは含まれません。

## <a name="build-11902069"></a>ビルド 1.1902.0.69

### <a name="new-features"></a>新機能

- 1902 ビルドでは、Azure Stack の管理者ポータル上にプラン、オファー、クォータ、アドオン プランの作成のための新しいユーザー インターフェイスを導入しています。 スクリーンショットを含めた詳細については、[プラン、オファー、クォータの作成](../azure-stack-create-plan.md)に関するページを参照してください。

- このリリースでのその他の変更点および機能強化点の一覧は、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1902.md#improvements)を参照してください。

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>既知の問題

- 内部ロード バランサー (ILB) を宛先としたパケットが 1,450 バイトを上回る場合にドロップする問題が発見されています。 この問題は、ホストの MTU の設定が、ロールを走査するカプセル化された VXLAN パケットに対応できないほど低い値になっていることが原因となって発生します。この設定は、1901 の時点でホストに移行されました。 この問題が発生することが確認されているシナリオは少なくとも 2 つあります。

  - 内部ロード バランサー (ILB) の背後にある SQL Always-On に SQL クエリを実行し、そのクエリが 660 バイトを超えるとき。
  - 複数のマスターを有効にしようとした場合に、Kubernetes のデプロイに失敗したとき。  

  この問題は、同じ仮想ネットワークにあるものの、サブネットが異なる VM と ILB の間に通信がある場合に発生します。 ASDK ホスト上で管理者特権でのコマンド プロンプトを起動し、次のコマンドを実行すると、この問題を回避できます。

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>ビルド 1.1901.0.95

[Azure Stack リリースノート内の重要なビルド情報](../azure-stack-update-1901.md#build-reference)を参照してください。

### <a name="changes"></a>変更点

このビルドには、Azure Stack に対する次の機能強化が含まれています。

- 物理ホストに BGP および NAT コンポーネントがデプロイされるようになりました。 これにより、ASDK をデプロイするために 2 つのパブリックまたは企業 IP アドレスを持つ必要がなくなり、デプロイも簡略化されます。
- **asdk-installer.ps1** PowerShell スクリプトを使用して、Azure Stack 統合システムのバックアップを[検証](asdk-validate-backup.md)できるようになりました。

### <a name="new-features"></a>新機能

- このリリースでの新機能の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1901.md#new-features)を参照してください。

### <a name="fixed-and-known-issues"></a>修正された問題と既知の問題

- このリリースで修正された問題の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1901.md#fixed-issues)を参照してください。 既知の問題の一覧については、[このセクション](../azure-stack-update-1901.md#known-issues-post-installation)を参照してください。
- [使用可能な Azure Stack 修正プログラム](../azure-stack-update-1901.md#azure-stack-hotfixes)が Azure Stack ASDK には適用できないことに注意してください。

## <a name="build-118110101"></a>ビルド 1.1811.0.101

### <a name="changes"></a>変更点

このビルドには、Azure Stack に対する次の機能強化が含まれています。  

- ASDK に使用するハードウェアとソフトウェアに関して、新しい最小要件と推奨要件が存在します。 そうした新しい推奨仕様は、「[Azure Stack のデプロイ計画に関する考慮事項](asdk-deploy-considerations.md)」に記載されています。 Azure Stack プラットフォームの進化に合わせて、提供されるサービスが増え、必要なリソースも増えていくと考えられます。 推奨情報の改訂は、こうした仕様の高まりを反映したものです。

### <a name="new-features"></a>新機能

このリリースでの新機能の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1811.md#new-features)を参照してください。

### <a name="fixed-and-known-issues"></a>修正された問題と既知の問題

このリリースで修正された問題の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1811.md#fixed-issues)を参照してください。 このリリースでの既知の問題の一覧については、[このセクション](../azure-stack-update-1811.md#known-issues-post-installation)を参照してください。
