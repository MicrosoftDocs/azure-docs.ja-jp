---
title: Azure Firewall を使用して Windows Virtual Desktop を保護する
description: Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する方法を確認する
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ad4f42d0e33f6d70c75abfcd1daab4f5aa9a515f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94654985"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Azure Firewall を使用して Window Virtual Desktop のデプロイを保護する

Windows Virtual Desktop は、Azure で実行されるデスクトップおよびアプリの仮想化サービスです。 エンド ユーザーが Windows Virtual Desktop 環境に接続すると、そのセッションはホスト プールによって実行されます。 ホスト プールとは、Windows Virtual Desktop にセッション ホストとして登録する Azure 仮想マシンのコレクションです。 これらの仮想マシンは仮想ネットワーク内で実行され、仮想ネットワークのセキュリティ制御の対象となります。 Windows Virtual Desktop サービスへの発信インターネット アクセスが正常に動作している必要があり、エンド ユーザー向けに送信インターネット アクセスが必要な場合もあります。 Azure Firewall は、環境をロックダウンし、送信トラフィックをフィルター処理するのに役立ちます。

[ ![Windows Virtual Desktop のアーキテクチャ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

この記事のガイドラインに従って、Azure Firewall を使用して、Windows Virtual Desktop ホスト プールの保護を強化します。

## <a name="prerequisites"></a>前提条件


 - デプロイされた Windows Virtual Desktop 環境とホスト プール

   詳細については、「[チュートリアル:Azure Marketplace を使用してホスト プールを作成する](../virtual-desktop/create-host-pools-azure-marketplace.md)」および「[Azure Resource Manager テンプレートを使用してホスト プールを作成する](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)」を参照してください。

Windows Virtual Desktop 環境の詳細については、「[Windows Virtual Desktop 環境](../virtual-desktop/environment-setup.md)」をご覧ください。

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Windows Virtual Desktop へのホスト プールの送信アクセス

Windows Virtual Desktop 用に作成する Azure 仮想マシンが正常に機能するためには、いくつかの完全修飾ドメイン名 (FQDN) へのアクセス権が必要です。 Azure Firewall には、この構成を簡略化するための Windows Virtual Desktop FQDN タグが用意されています。 Windows Virtual Desktop プラットフォームの送信トラフィックを許可するには、次の手順に従います。

- Azure Firewall 経由ですべてのトラフィックをルーティングするように、Azure Firewall をデプロイし、Windows Virtual Desktop ホスト プールのサブネットユーザー定義ルート (UDR) を構成します。 これで、既定のルートがファイアウォールを指すようになりました。
- アプリケーション ルール コレクションを作成し、*WindowsVirtualDesktop* FQDN タグを有効にするルールを追加します。 発信元 IP アドレスの範囲はホスト プールの仮想ネットワークで、プロトコルは **https**、宛先は **WindowsVirtualDesktop** です。

- Windows Virtual Desktop ホスト プールに必要な一連のストレージ アカウントと Service Bus アカウントはデプロイ固有であるため、WindowsVirtualDesktop の FQDN タグにはまだキャプチャされていません。 これは、次の方法のいずれかの方法で対処できます。

   - ホスト プールサブネットから *xt.blob.core.windows.net、*eh.servicebus.windows.net、および *xt.table.core.windows.net への https アクセスを許可します。 これらのワイルドカード FQDN によって必要なアクセスが有効になりますが、制限は緩くなります。
   - 次の Log Analytics クエリを使用して、必要な FQDN を正確に一覧表示してから、ファイアウォール アプリケーション ルールでそれらを明示的に許可します。
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- ネットワーク ルール コレクションを作成して次のルールを追加します。

   - DNS を許可 – TCP および UDP ポート 53 の * に、ADDS プライベート IP アドレスからのトラフィックを許可します。
   - KMS を許可 – Windows Virtual Desktop 仮想マシンから Windows Activation Service の TCP ポート 1688 へのトラフィックを許可します。 宛先 IP アドレスの詳細については、「[強制トンネリングを使用したライセンス認証の問題](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution)」を参照してください。

> [!NOTE]
> 一部のデプロイでは、DNS ルールを必要としない場合があります。たとえば Azure Active Directory ドメイン コントローラーは、DNS クエリを 168.63.129.16 の Azure DNS に転送します。

## <a name="host-pool-outbound-access-to-the-internet"></a>インターネットへのホスト プールの送信アクセス

組織のニーズに応じて、エンド ユーザーに対してセキュリティで保護された発信インターネット アクセスを有効にすることをお勧めします。 許可される送信先の一覧が適切に定義されている場合 ([Microsoft 365 へのアクセス](/microsoft-365/enterprise/microsoft-365-ip-web-service)など)、Azure Firewall アプリケーションとネットワーク ルールを使用して必要なアクセスを構成できます。 これにより、最適なパフォーマンスを得るため、エンド ユーザーのトラフィックがインターネットに直接ルーティングされます。

既存のオンプレミス セキュリティで保護された Web ゲートウェイを使用して送信ユーザーのインターネット トラフィックをフィルター処理する場合は、明示的なプロキシ構成を使用して、Windows Virtual Desktop ホスト プールで実行されている Web ブラウザーやその他のアプリケーションを構成できます。 例については、「[Microsoft Edge コマンド ライン オプションを使用してプロキシ設定を構成する方法](/deployedge/edge-learnmore-cmdline-options-proxy-settings)」を参照してください。 これらのプロキシ設定は、エンド ユーザーのインターネットアクセスにのみ影響し、Azure Firewall 経由で直接 Windows Virtual Desktop プラットフォームの送信トラフィックを許可します。

## <a name="additional-considerations"></a>その他の注意点

要件に応じて、追加のファイアウォール規則を構成することが必要になる場合があります。

- NTP サーバー アクセス

   既定では、Windows を実行している仮想マシンは、時間を同期するために UDP ポート 123 経由で time.windows.com に接続します。 このアクセスを許可するネットワーク ルール、または環境内で使用するタイム サーバーを作成します。


## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop について、さらに詳しく学習します。[Windows Virtual Desktop とは](../virtual-desktop/overview.md)