---
title: "Azure Stack での更新プログラムの管理概要 | Microsoft Docs"
description: "Azure Stack 統合システムの更新プログラムの管理について説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Azure Stack での更新プログラムの管理概要

*適用対象: Azure Stack 統合システム*

Microsoft では、Azure Stack 統合システムの更新プログラム パッケージを定期的にリリースします。 Microsoft ソフトウェア更新プログラムの各リリースは、1 つの更新プログラム パッケージとしてバンドルされます。 Azure Stack のオペレーターは、これらの更新プログラム パッケージのインポート、インストール、そのインストール状況の監視を、管理者ポータルから簡単に行うことができます。 

また、お使いの OEM (Original Equipment Manufacturer) ハードウェア ベンダーからも、ドライバーやファームウェアの更新などの更新プログラムがリリースされます。 これらの更新プログラムは、OEM ハードウェア ベンダーによって別パッケージとして配布され、Microsoft の更新プログラムとは別に管理されます。

お使いのシステムのサポートを維持するには、Azure Stack を継続的にアップデートして常に特定のバージョン レベルを保つようにする必要があります。 必ず「[Azure Stack servicing policy (Azure Stack サービス ポリシー)](azure-stack-servicing-policy.md)」を確認してください。

> [!NOTE]
> Azure Stack Development Kit に Azure Stack 更新プログラム パッケージを適用することはできません。 更新プログラム パッケージは統合システム用に設計されています。

## <a name="the-update-resource-provider"></a>更新リソースプロバイダー

Azure Stack には、Microsoft ソフトウェア更新プログラムの適用を調整する更新リソースプロバイダーが含まれています。 このリソースプロバイダーによって、すべての物理ホスト、Service Fabric アプリケーションとランタイム、すべてのインフラストラクチャ仮想マシンとその関連サービスに更新プログラムが適用されていることが確認されます。

更新プログラムをインストールすると、更新プロセスが Azure Stack 内のさまざまなサブシステム (例: 物理ホスト、インフラストラクチャ仮想マシン) をターゲットとしていく状況の概要を簡単に確認できます。

## <a name="plan-for-updates"></a>更新の計画

メンテナンス操作についてユーザーに通知することと、通常のメンテナンス期間はできるだけ勤務時間外にスケジュールすることを強くお勧めします。 メンテナンス操作は、テナントのワークロードとポータル操作の両方に影響を及ぼす可能性があります。

## <a name="using-the-update-tile-to-manage-updates"></a>[更新] タイルによる更新プログラムの管理
管理者ポータルから更新プログラムを管理するプロセスは、簡単です。 Azure Stack オペレーターは、ダッシュボード内の [更新] タイルに移動して、以下のことができます。

- 現在のバージョンなどの重要な情報を表示します。
- 更新プログラムをインストールし、進行状況を監視します。
- 以前にインストールされた更新プログラムの更新履歴を確認します。
 
## <a name="determine-the-current-version"></a>現在のバージョンの判断

更新タイルは、Azure Stack の現在のバージョンを示します。 更新タイルは、管理者ポータルで、次のいずれかの方法で表示することができます。

- ダッシュボードの **[更新]** タイルで現在のバージョンを表示します。
 
   ![既定のダッシュボードの [更新] タイル](./media/azure-stack-updates/image1.png)
 
- **[リージョンの管理]** タイルで、リージョン名をクリックします。 **[更新]** タイルで現在のバージョンを表示します。

## <a name="next-steps"></a>次のステップ

- [Azure Stack サービス ポリシー](azure-stack-servicing-policy.md) 
- [Azure Stack でのリージョンの管理](azure-stack-region-management.md)     


