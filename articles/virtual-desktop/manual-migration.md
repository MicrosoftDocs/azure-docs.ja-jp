---
title: Windows Virtual Desktop (クラシック) から手動で移行する - Azure
description: Windows Virtual Desktop (クラシック) から Windows Virtual Desktop に手動で移行する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516173"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) から手動で移行する

Windows Virtual Desktop (クラシック) では、PowerShell コマンドレット、REST API、サービス オブジェクトを使用してそのサービス環境が作成されます。 Windows Virtual Desktop サービス環境の "オブジェクト" は、Windows Virtual Desktop で作成されるものです。 サービス オブジェクトには、テナント、ホスト プール、アプリケーション グループ、セッション ホストが含まれます。

ただし、Windows Virtual Desktop (クラシック) は Azure と統合されていません。 Azure 統合なしの場合、作成したオブジェクトは、Azure サブスクリプションに接続されないため、Azure portal で自動的に管理されません。

Windows Virtual Desktop の最新のメジャー アップデートでは、完全な Azure 統合に向けたサービスのシフトが示されています。 Windows Virtual Desktop で作成したオブジェクトは、Azure portal によって自動的に管理されます。

この記事では、最新バージョンの Windows Virtual Desktop への移行を検討する必要がある理由について説明します。 その後、Windows Virtual Desktop (クラシック) から Windows Virtual Desktop の最新アップデートに手動で移行する方法について説明します。

## <a name="why-migrate"></a>移行する理由

メジャー アップデートは不便な場合があります (特に手動でメジャー アップデートを実行する必要がある場合)。 ただし、次のように自動的に移行できない理由がいくつかあります。

- クラシック リリースで作成された既存のサービス オブジェクトは、Azure では表現されません。 そのスコープは、Windows Virtual Desktop サービスを超えて拡張されません。
- 最新のアップデートでは、サービスのアプリケーション ID が変更され、Windows Virtual Desktop (クラシック) で行われていた方法でのアプリに関する同意が削除されました。 新しいアプリケーション ID で認証されている場合を除き、Windows Virtual Desktop で新しい Azure オブジェクトは作成できません。

ただし、クラシック バージョンからの移行は依然として重要です。 移行後に実行できる操作は次のとおりです。

- Azure portal を使用して Windows Virtual Desktop を管理する。
- アプリケーション グループに Azure Active Directory (AD) ユーザー グループを割り当てる。
- 強化された Log Analytics 機能を使用して、デプロイをトラブルシューティングする。
- Azure ネイティブのロールベースのアクセス制御 (Azure RBAC) を使用して、管理アクセスを管理する。

## <a name="when-should-i-migrate"></a>移行するタイミング

移行する必要があるかどうかを確認する場合は、デプロイの現在と将来の状況も考慮する必要があります。

次のようないくつかのシナリオでは特に、手動で移行することをお勧めします。

- 少数のユーザーによるテスト ホスト プールが設定されている。
- 少数のユーザーによる実稼働ホスト プールが設定されているが、最終的には数百のユーザーまで増やす予定である。
- 簡単にレプリケートできる単純なセットアップを使用している。 たとえば、VM でギャラリー イメージを使用している場合などです。

> [!IMPORTANT]
> 安定するまでに時間がかかる、またはユーザー数が多い高度な構成を使用している場合は、手動で移行することはお勧めしません。

## <a name="prepare-for-migration"></a>移行を準備する

作業を開始する前に、環境を移行する準備ができていることを確認する必要があります。

移行プロセスを開始するには、以下が必要です。

- 新しい Azure サービス オブジェクトを作成する Azure サブスクリプション。
- 次のロールに割り当てられていることを確認します。
    
    - Contributor
    - User Access Administrator
    
    共同作成者ロールを使用すると、サブスクリプションで Azure オブジェクトを作成できます。ユーザー アクセス管理者ロールを使用すると、ユーザーをアプリケーション グループに割り当てることができます。

## <a name="how-to-migrate-manually"></a>手動で移行する方法

移行プロセスの準備ができたので、実際に移行します。

Windows Virtual Desktop (クラシック) から Windows Virtual Desktop に手動で移行するには、次のようにします。

1. 「[Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」の手順に従い、Azure portal ですべての高レベル オブジェクトを作成します。
2. 既に使用している仮想マシンを持ち込む場合は、「[Windows Virtual Desktop ホスト プールに仮想マシンを登録する](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)」の手順に従い、手順 1 で作成した新しいホスト プールにその仮想マシンを手動で登録します。
3. 新しい RemoteApp アプリ グループを作成します。
4. 新しいデスクトップおよび RemoteApp アプリ グループにユーザーまたはユーザー グループを発行します。
5. [多要素認証の設定](set-up-mfa.md)に関するページの手順に従い、新しいオブジェクトを許可するように条件付きアクセス ポリシーを更新します。

ダウンタイムを回避するために、最初に、既存のセッション ホストを小さいグループで Azure Resource Manager 統合ホスト プールに一度に登録する必要があります。 その後、新しい Azure Resource Manager 統合アプリ グループにユーザーを徐々に移動できます。

## <a name="next-steps"></a>次の手順

移行が完了した後、[チュートリアル](create-host-pools-azure-marketplace.md)を確認して、Windows Virtual Desktop がどのように動作するかを理解します。 「[既存のホスト プールを展開する](expand-existing-host-pool.md)」と[RDP のプロパティのカスタマイズ](customize-rdp-properties.md)に関する記事で、高度な管理機能について学習します。

サービス オブジェクトの詳細については、「[Windows Virtual Desktop の環境](environment-setup.md)」を確認してください。
