---
title: SSIS 統合ランタイムで接続の診断機能を使用する
description: 接続の診断機能を使用して、SSIS 統合ランタイムでの接続の問題をトラブルシューティングします。
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361861"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>SSIS 統合ランタイムで接続の診断機能を使用する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SSIS 統合ランタイムで SQL Server Integration Services (SSIS) パッケージの実行中に接続の問題が見つかる可能性があります。 これらの問題は、SSIS 統合ランタイムが Azure 仮想ネットワークに参加している場合に特に発生します。

接続をテストするための "*接続の診断*" 機能を使用して、接続の問題をトラブルシューティングします。 この機能は、Azure Data Factory ポータルの SSIS 統合ランタイムの監視ページにあります。

 ![監視ページ - 接続の診断](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![監視ページ - 接続のテスト](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

以降のセクションでは、接続をテストしているときに発生する最も一般的なエラーについて説明します。 各セクションでは、次のことについて説明します。

- エラー コード
- エラー メッセージ
- エラーの考えられる原因
- 推奨されるソリューション

## <a name="error-code-invalidinput"></a>エラー コード:InvalidInput

- **エラー メッセージ**:"Please verify your input is correct." (入力が正しいことを確認してください。)
- **Potential cause (潜在的な原因):** 入力が正しくない。
- **推奨事項**:入力を確認してください。

## <a name="error-code-firewallornetworkissue"></a>エラー コード:FirewallOrNetworkIssue

- **エラー メッセージ**:"Please verify that this port is open on your firewall/server/NSG and the network is stable." (ファイアウォール、サーバー、または NSG でこのポートが開いていること、およびネットワークが安定していることを確認してください。)
- **考えられる原因:**
  - サーバーでポートが開かれていない。
  - ポートでのアウトバウンド トラフィックが、ネットワーク セキュリティグ ループによって拒否された。
  - NVA、Azure Firewall、またはオンプレミスのファイアウォールで、このポートが開かれていない。
- **推奨事項:**
  - サーバーでこのポートを開きます。
  - このポートでアウトバウンド トラフィックが許可されるように、ネットワーク セキュリティグ ループを更新します。
  - NVA、Azure Firewall、またはオンプレミスのファイアウォールで、このポートを開きます。

## <a name="error-code-misconfigureddnssettings"></a>エラー コード:MisconfiguredDnsSettings

- **エラー メッセージ**:"If you’re using your own DNS server in the VNet joined by your Azure-SSIS IR, verify that it can resolve your host name." (Azure-SSIS IR が参加している VNet で独自の DNS サーバーを使用している場合は、ホスト名を解決できることを確認します。)
- **考えられる原因:**
  -  カスタム DNS に問題がある。
  -  プライベート ホスト名に完全修飾ドメイン名 (FQDN) が使用されていない。
- **推奨事項:**
  -  カスタム DNS の問題を修正して、ホスト名を解決できることを確認してください。
  -  FQDN を使用します。 独自の DNS サフィックスは、Azure-SSIS IR によって自動的に追加されません。 たとえば、 **<your_private_server>** ではなく、 **<your_private_server>.contoso.com** を使用します。

## <a name="error-code-servernotallowremoteconnection"></a>エラー コード:ServerNotAllowRemoteConnection

- **エラー メッセージ**:"Please verify that your server allows remote TCP connections through this port." (このポートを介したリモート TCP 接続がサーバーで許可されていることを確認してください。)
- **考えられる原因:**
  -  サーバーのファイアウォールで、リモート TCP 接続が許可されていない。
  -  サーバーがオンラインではない。
- **推奨事項:**
  -  サーバーのファイアウォールでリモート TCP 接続を許可します。
  -  サーバーを起動します。
   
## <a name="error-code-misconfigurednsgsettings"></a>エラー コード:MisconfiguredNsgSettings

- **エラー メッセージ**:"Please verify that the NSG of your VNet allows outbound traffic through this port." (このポートを介したアウトバウンド トラフィックが VNet の NSG で許可されていることを確認してください。) "If you’re using Azure ExpressRoute and or a UDR, please verify that this port is open on your firewall/server." (Azure ExpressRoute または UDR を使用している場合は、このポートがファイアウォールまたはサーバーで開かれていることを確認してください。)
- **考えられる原因:**
  -  ポートでのアウトバウンド トラフィックが、ネットワーク セキュリティグ ループによって拒否された。
  -  NVA、Azure Firewall、またはオンプレミスのファイアウォールで、このポートが開かれていない。
- **推奨事項:**
  -  このポートでアウトバウンド トラフィックが許可されるように、ネットワーク セキュリティグ ループを更新します。
  -  NVA、Azure Firewall、またはオンプレミスのファイアウォールで、このポートを開きます。

## <a name="error-code-genericissues"></a>エラー コード:GenericIssues

- **エラー メッセージ**:"Test connection failed due to generic issues." (一般的な問題が原因でテスト接続に失敗しました。)
- **Potential cause (潜在的な原因):** テスト接続で一時的に一般的な問題が発生した。
- **推奨事項**:後でテスト接続を再試行します。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

## <a name="error-code-pspingexecutiontimeout"></a>エラー コード:PSPingExecutionTimeout

- **エラー メッセージ**:"Test connection timeout, please try again later." (テスト接続がタイムアウトしました。後でもう一度お試しください。)
- **Potential cause (潜在的な原因):** テスト接続がタイムアウトした。
- **推奨事項**:後でテスト接続を再試行します。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

## <a name="error-code-networkinstable"></a>エラー コード:NetworkInstable

- **エラー メッセージ**:"Test connection irregularly succeeded due to network instability." (ネットワークが不安定なため、テスト接続が不規則にしか成功しませんでした。)
- **Potential cause (潜在的な原因):** 一時的なネットワークの問題。
- **推奨事項**:サーバーまたはファイアウォール ネットワークが安定しているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

- [SSMS を使用して Azure に SSIS プロジェクトを配置する](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [SSMS を使用して Azure で SSIS パッケージを実行する](/sql/integration-services/ssis-quickstart-run-ssms)
- [Azure で SSIS パッケージをスケジュール設定する](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)