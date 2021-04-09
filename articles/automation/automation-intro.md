---
title: Azure Automation の概要
description: この記事では、Azure Automation の概要、および Azure Automation を使用してインフラストラクチャとアプリケーションのライフサイクルを自動化する方法を説明します。
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, Update Management, Change Tracking, DSC, インベントリ, Runbook, Python, グラフィカル
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182820"
---
# <a name="an-introduction-to-azure-automation"></a>Azure Automation の概要

Azure Automation は、Azure 環境と非 Azure 環境を一貫性をもって管理するクラウドベースのオートメーションと構成サービスを提供します。 プロセスの自動化、構成管理、更新の管理、共有機能、異種環境機能で構成されます。 Automation は、ワークロードとリソースのデプロイ、運用、使用停止を完全に制御します。

![自動化の機能](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>プロセスの自動化

頻繁に発生し、時間がかかり、エラーが発生しやすいクラウド管理タスクは、Azure Automation のプロセス オートメーションで自動化することができます。 このサービスを利用して、ビジネスの価値を高める作業に集中できます。 また、エラーを減らして効率性を向上できるため、運用コストの削減にも役立ちます。 プロセス オートメーションの運用環境について詳しくは、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。

プロセス オートメーションでは、エンド ツー エンドのプロセスのデプロイ、構成、管理に必要なパブリック システムと Azure サービスとを統合することができます。 このサービスでは、[Runbook の作成](automation-runbook-types.md)をグラフィカルに行うか、PowerShell または Python を使用して実行できます。 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) を使用して、オンプレミス環境間で調整することで管理を一元化できます。 要求は [Webhook](automation-webhooks.md) によって実行することができます。ITSM、DevOps、監視システムからオートメーションをトリガーすることによって継続的デリバリーと連続動作が保証されます。 

## <a name="configuration-management"></a>構成管理

Azure Automation の構成管理では、次の 2 つの機能にアクセスできます。

* 変更履歴とインベントリ
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>変更履歴とインベントリ

変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。 この機能の詳細については、[変更履歴とインベントリ](change-tracking/overview.md)に関する記事を参照してください。

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Azure Automation State Configuration](automation-dsc-overview.md) は、PowerShell DSC (Desired State Configuration) 用のクラウドベースの機能であり、エンタープライズ環境に必要なサービスを備えています。 この機能を使用すると、DSC リソースを Azure Automation で管理し、Azure クラウド内の DSC プル サーバーから仮想マシンおよび物理マシンに構成を適用できます。 

## <a name="update-management"></a>更新管理

Azure Automation には、Windows システムと Linux システムのハイブリッド環境全体に対する[更新管理](./update-management/overview.md)機能が含まれています。 更新管理を使用することで、Azure を始めとするクラウドとオンプレミスの垣根を越えて更新の準拠状態を可視化することができます。 この機能を使用すると、デプロイ スケジュールを作成して、定義済みのメンテナンス期間中に更新プログラムがインストールされるように調整できます。 マシンにインストールすべきでない更新プログラムは、更新管理機能を使用して、デプロイから除外することができます。

## <a name="shared-capabilities"></a>共有機能

Azure Automation には、共有リソース、ロールベースのアクセス制御、柔軟なスケジューリング、ソース管理統合、監査、タグ付けなど、さまざまな共有機能が用意されています。

### <a name="shared-resources"></a><a name="shared-resources"></a>共有リソース

Azure Automation は、環境を大きな規模で容易に自動化して構成できるようにする一連の共有リソースで構成されます。

* **[スケジュール](./shared-resources/schedules.md)** - あらかじめ定義されている時刻にオートメーション操作をトリガーします。
* **[モジュール](./shared-resources/modules.md)** - Azure やその他のシステムを管理します。 モジュールは、Microsoft、サード パーティ、コミュニティ、カスタム定義コマンドレット、および DSC リソース用の Automation アカウントにインポートできます。
* **[モジュール ギャラリー](automation-runbook-gallery.md)** - PowerShell ギャラリーとのネイティブな統合をサポートします。Runbook を表示してそれらを Automation アカウントにインポートすることができます。 PowerShell ギャラリーと Microsoft スクリプト センターからプロセスの統合と作成をすぐに開始できます。
* **[Python 2 パッケージ](python-packages.md)** - Automation アカウントでは、Python 2 Runbook がサポートされます。
* **[資格情報](./shared-resources/credentials.md)** - Runbook と構成で実行時に使用される可能性がある秘匿性の高い情報を安全に格納します。
* **[接続](automation-connections.md)** - システムへの接続に使用される一般的な情報の名前と値の組を格納します。 実行時に Runbook と構成内で使用される接続をモジュールの作成者が定義します。
* **[証明書](./shared-resources/certificates.md)** - デプロイされたリソースに対し、Runbook または DSC 構成が実行時にアクセスする際の認証とセキュリティ保護に使用される情報を定義します。 
* **[変数](./shared-resources/variables.md)** - Runbook と構成間で使用できるコンテンツを保持します。 Runbook またはそれらを参照する構成を変更することなく、変数の値を変更できます。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Automation では、Automation アカウントとそのリソースへのアクセスを制限する Azure ロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Automation アカウント、Runbook、ジョブに対する Azure RBAC の構成について詳しくは、[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)に関するページを参照してください。

### <a name="source-control-integration"></a>ソース管理の統合

Azure Automation では、[ソース管理の統合](source-control-integration.md)がサポートされます。 Runbook または構成をソース管理システムにチェックインできる場面で、構成をコードに昇格させることができます。

## <a name="heterogeneous-support-windows-and-linux"></a>異種環境のサポート (Windows と Linux)

Automation は、ハイブリッド クラウド環境で動作するほか、Windows と Linux が混在するシステムでも動作するように設計されています。 デプロイされたワークロードとそれらが実行されるオペレーティング システムとを自動化、構成する一貫した方法が実現されています。

## <a name="common-scenarios-for-automation"></a>Automation の一般的なシナリオ

Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 一般的なシナリオは、次のとおりです。

* **Runbook の作成** - PowerShell、PowerShell ワークフロー、グラフィカル、Python 2、DSC の Runbook を共通の言語で作成します。 
* **リソースのビルドとデプロイ** - Runbook と Azure Resource Manager テンプレートを使用して、ハイブリッド環境全体に仮想マシンをデプロイします。 Jenkins や Azure DevOps などの開発ツールに統合します。
* **VM の構成** - インフラストラクチャとアプリケーションに適した構成を使用して Windows マシンと Linux マシンを評価、構成します。
* **知識の共有** - 組織がワークロードを提供して管理する方法に関する知識をシステムに転送します。 
* **インベントリの取得** - ターゲット設定、レポート作成、コンプライアンスを目的に、デプロイされたリソースの完全なインベントリを取得します。 
* **変更の検出** - 構成エラーを引き起こす可能性がある変更を識別し、運用上のコンプライアンスを向上させます。
* **監視** - 問題の原因となっているマシンの変更を特定し、修復するか管理システムにエスカレートします。
* **保護する** - セキュリティ アラートが発生した場合にマシンを検疫します。 ゲスト要件を設定します。
* **設定** - チーム用の Azure RBAC を設定します。 未使用のリソースを回収します。

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Azure Automation の価格

Azure Automation に関する価格は、[価格](https://azure.microsoft.com/pricing/details/automation/)ページで確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Automation アカウントを作成する](automation-quickstart-create-account.md)