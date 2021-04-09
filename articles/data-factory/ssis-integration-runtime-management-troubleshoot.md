---
title: SSIS Integration Runtime 管理のトラブルシューティング
description: この記事では、SSIS Integration Runtime (SSIS IR) の管理に関する問題のトラブルシューティング ガイダンスを提供します
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361844"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory で SSIS Integration Runtime 管理のトラブルシューティングを行う

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、SSIS IR とも呼ばれる Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) の管理の問題に関するトラブルシューティングのガイダンスを示します。

## <a name="overview"></a>概要

SSIS IR のプロビジョニングまたはプロビジョニング解除中に問題が発生した場合は、Microsoft Azure Data Factory ポータルにエラー メッセージが表示されるか、PowerShell コマンドレットからエラーが返されます。 エラーの表示は常に、エラー コードと詳細なエラー メッセージが表示される形式になります。

エラーコードが InternalServerError の場合、サービスに一時的な問題があるため、後で操作を再試行する必要があります。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

それ以外の場合、次の 3 つの主な外部依存関係によってエラーが発生する可能性があります。つまり、Azure SQL Database または Azure SQL Managed Instance、カスタム セットアップ スクリプト、および仮想ネットワーク構成です。

## <a name="sql-database-or-sql-managed-instance-issues"></a>SQL Database または SQL Managed Instance の問題

SSIS カタログ データベースを使用して SSIS IR をプロビジョニングする場合、SQL Database または SQL Managed Instance が必要です。 SSIS IR は SQL Database または SQL Managed Instance にアクセスできる必要があります。 また、SQL Database または SQL Managed Instance のログイン アカウントには、SSIS カタログ データベース (SSISDB) を作成するためのアクセス許可が必要です。 エラーが発生した場合は、エラー コードと詳細な SQL 例外メッセージが Data Factory ポータルに表示されます。 エラー コードのトラブルシューティングを行うには、次の一覧の情報を使用します。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

この問題は、新しい SSIS IR をプロビジョニングしているか、IR の実行中に発生することがあります。 IR プロビジョニング中にこのエラーが発生した場合は、次のいずれかの問題を示すエラー メッセージに詳細な SqlException メッセージが表示されることがあります。

* ネットワーク接続の問題。 SQL Database または SQL Managed Instance のホスト名にアクセスできるかどうかを確認します。 また、ファイアウォールまたはネットワーク セキュリティ グループ (NSG) によって、サーバーへの SSIS IR アクセスがブロックされていないことを確認します。
* SQL 認証中にログインに失敗しました。 指定されたアカウントは SQL Server にサインインできません。 正しいユーザー アカウントが指定されていることを確認します。
* Microsoft Azure Active Directory (Azure AD) 認証 (マネージド ID) の実行中にログインに失敗しました。 ファクトリのマネージド ID を AAD グループに追加し、マネージド ID にカタログ データベース サーバーへのアクセス許可を付与してください。
* 接続のタイムアウト。 このエラーは、常にセキュリティ関連の構成が原因で発生します。 推奨事項は次のとおりです。
  1. 新しい VM を作成します。
  1. IR が仮想ネットワーク内にある場合は、同じ Microsoft Azure Virtual Network の IR に VM を参加させます。
  1. SSMS をインストールし、SQL Database または SQL Managed Instance の状態を確認します。

その他の問題については、詳細な SQL 例外エラー メッセージに示されている問題を修正してください。 まだ問題が発生している場合は、SQL Database または SQL Managed Instance のサポート チームにお問い合わせください。

IR の実行中にこのエラーが発生する場合は、おそらくネットワーク セキュリティ グループまたはファイアウォールの変更が原因で、SSIS IR ワーカー ノードが SQL Database または SQL Managed Instance にアクセスできなくなっています。 SSIS IR ワーカー ノードをブロック解除して、SQL Database または SQL Managed Instance にアクセスできるようにしてください。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

この種類のエラー メッセージは次のようになります。"データベース 'SSISDB' のサイズ クォータに達しました。 データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。" 

次のような解決策が考えられます。
* SSISDB のクォータ サイズを増やす。
* SSISDB の構成を変更して、次のようにサイズを小さくする。
   * 保有期間とプロジェクト バージョンの数を減らす。
   * ログの保有期間を短縮する。
   * ログの既定レベルを変更する。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

このエラーは、SQL Database または SQL Managed Instance が既に SSISDB を持ち、それが別の IR によって使用されていることを意味します。 別の SQL Database または SQL Managed Instance を指定するか、既存の SSISDB を削除して新しい IR を再起動する必要があります。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

このエラーは、次のいずれかの理由で発生することがあります。

* SSIS IR 用に構成されているユーザー アカウントに、データベースを作成するためのアクセス許可がない。 データベースを作成する権限をユーザーに付与できます。
* データベースの作成中にタイムアウトが発生する。たとえば、実行タイムアウトや DB 操作のタイムアウトなどです。 後で操作を再試行してください。 再試行しても解決できない場合は、SQL Database または SQL Managed Instance のサポート チームにお問い合わせください。

その他の問題については、SQL 例外のエラー メッセージを確認し、エラーの詳細に記載されている問題を修正してください。 まだ問題が発生している場合は、SQL Database または SQL Managed Instance のサポート チームにお問い合わせください。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

この種のエラー メッセージは次のようになります。"オブジェクト名 'catalog.catalog_properties' が無効です。"このような状況では、SSISDB という名前のデータベースが既にあるが、SSIS IR によって作成されていないか、前回の SSIS IR プロビジョニングでのエラーが原因でデータベースが無効な状態になっています。 SSISDB という名前の既存のデータベースを削除したり、IR 用の新しい SQL Database または SQL Managed Instance を構成したりすることができます。

## <a name="custom-setup-issues"></a>カスタム セットアップの問題

カスタム セットアップでは、お使いの SSIS IR のプロビジョニングまたは再構成の間に、独自のセットアップ手順を追加するためのインターフェイスを提供します。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

必要なカスタム セットアップ ファイルのみがコンテナーに含まれていることを確認してください。コンテナー内のすべてのファイルが SSIS IR ワーカー ノードにダウンロードされます。 SSIS IR でスクリプトを実行する前に、ローカル コンピューターでカスタム セットアップ スクリプトをテストして、スクリプトの実行に関する問題を修正することをお勧めします。

SSIS IR は定期的に更新されるため、IR の実行中はカスタム セットアップ スクリプト コンテナーがチェックされます。 この更新では、カスタム セットアップ スクリプトをダウンロードして再インストールするために、コンテナーへのアクセスが必要になります。 この処理では、コンテナーがアクセス可能かどうか、および main.cmd ファイルが存在するかどうかもチェックされます。

カスタム セットアップに関するエラーについては、CustomSetupScriptBlobContainerInaccessible や CustomSetupScriptNotFound のようなサブ コードを含む CustomSetupScriptFailure エラー コードが表示されます。

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

このエラーは、SSIS IR がカスタム セットアップのために Azure BLOB コンテナーにアクセスできないことを意味します。 コンテナーの SAS URI が到達可能で、期限切れになっていないことを確認します。

IR が実行中の状態の場合は、まず IR を停止し、新しいカスタム セットアップ コンテナーの SAS URI で IR を再構成してから、IR を再起動します。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

このエラーは、SSIS IR が BLOB コンテナーでカスタム セットアップ スクリプト (main.cmd) を見つけることができないことを意味します。 main.cmd がコンテナーに存在することを確認します。これは、カスタム セットアップ インストールのためのエントリ ポイントです。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

このエラーは、カスタム セットアップ スクリプト (main.cmd) の実行が失敗したことを意味します。 最初にローカル コンピューターでスクリプトを実行するか、BLOB コンテナーのカスタム セットアップ実行ログを確認してください。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

このエラーは、カスタム セットアップ スクリプトの実行のタイムアウトを示します。 スクリプトを自動的に実行でき、対話型の入力を必要としないこと、また、BLOB コンテナーに必要なカスタム セットアップ ファイルのみが含まれていることを確認してください。 まずは、ローカル コンピューター上でスクリプトをテストすることをお勧めします。 また、BLOB コンテナー内のカスタム セットアップ実行ログも確認する必要があります。 カスタム セットアップの最大期間は 45 分で、これを経過するとタイムアウトになります。この最大期間には、コンテナーからすべてのファイルをダウンロードし、SSIS IR のインストールにかかる時間が含まれます。 さらに長い期間が必要な場合は、サポート チケットを発行してください。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

このエラーは、カスタム セットアップ実行ログを BLOB コンテナーにアップロードしようとして失敗したことを意味します。 この問題は、SSIS IR が BLOB コンテナーに対する書き込みアクセス許可を持っていないか、ストレージまたはネットワークの問題が原因で発生します。 カスタム セットアップに成功した場合、このエラーは SSIS 機能には影響しませんが、ログがありません。 カスタム セットアップが別のエラーで失敗し、ログがアップロードされない場合は、このエラーを最初に報告して、分析のためにログをアップロードできるようにします。 また、この問題が解決された後、具体的な問題について報告いたします。 再試行後にこの問題が解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

## <a name="virtual-network-configuration"></a>Virtual Network の構成

SSIS IR を Azure Virtual Network に参加させると、SSIS IR はユーザー サブスクリプションにある仮想ネットワークを使用します。 詳細については、「[Azure-SSIS Integration Runtime を仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。
SSIS IR が正常に起動した後、ネットワーク接続の問題が発生した場合は、[診断接続ツール](ssis-integration-runtime-diagnose-connectivity-faq.md)を使用して、問題を自分で診断できます。
Virtual Network 関連の問題がある場合は、次のいずれかのエラーが表示されます。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

このエラーは、さまざまな理由で発生する可能性があります。 トラブルシューティングを行うには、「[Forbidden](#forbidden)」、「[InvalidPropertyValue](#invalidpropertyvalue)」、および「[MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings)」の各セクションを参照してください。

### <a name="forbidden"></a>Forbidden

この種のエラーは次のようになります。"現在のアカウントでは subnetId が有効になっていません。 Microsoft.Batch リソース プロバイダーが VNet の同じサブスクリプションに登録されていません。"

これらの詳細は、Azure Batch が仮想ネットワークにアクセスできないことを意味します。 Virtual Network と同じサブスクリプションに Microsoft Batch リソース プロバイダーを登録してください。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

この種のエラーは次のいずれかのようになります。 

- "指定された VNet が存在しないか、Batch サービスがそれにアクセスできません"
- "指定されたサブネット xxx は存在しません"

これらのエラーは、仮想ネットワークが存在しない、Azure Batch サービスがアクセスできない、または指定されたサブネットが存在しないことを意味します。 仮想ネットワークとサブネットが存在し、Azure Batch がこれらにアクセスできることを確認します。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

この種のエラー メッセージは次のようになります。"Vnet 内の統合ランタイムのプロビジョニングに失敗しました。 DNS サーバーまたは NSG の設定が構成されている場合は、DNS サーバーにアクセスできること、NSG が正しく構成されていることを確認してください。"

この状況では、DNS サーバーまたは NSG の設定の構成をカスタマイズしたことにより、SSIS IR に必要な Azure サーバー名を解決できないか、またはアクセスできなくなっている可能性があります。 詳細については、[SSIS IR 仮想ネットワークの構成](./join-azure-ssis-integration-runtime-virtual-network.md)に関する記事を参照してください。 問題が解決しない場合は、Azure Data Factory サポート チームにお問い合わせください。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR は定期的に自動的に更新されます。 アップグレード中に新しい Azure Batch プールが作成され、古い Azure Batch プールが削除されます。 また、古いプールの Virtual Network 関連のリソースが削除され、新しい Virtual Network 関連のリソースがサブスクリプションで作成されます。 このエラーは、サブスクリプションまたはリソース グループ レベルの削除ロックが原因で、古いプールの Virtual Network 関連リソースを削除できなかったことを意味します。 削除ロックの制御と設定はお客様が行うため、このような状況では削除ロックを解除する必要があります。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR プロビジョニングに失敗した場合、作成されたすべてのリソースが削除されます。 ただし、サブスクリプションまたはリソース グループ レベルでリソース削除ロックがある場合、Virtual Network リソースは想定どおりに削除されません。 このエラーを修正するには、削除ロックを解除し、IR を再起動してください。

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

SSIS IR を停止すると、Virtual Network に関連するすべてのリソースが削除されます。 ただし、サブスクリプションまたはリソース グループ レベルでリソース削除ロックがある場合、削除が失敗する可能性があります。 ここでも、削除ロックの制御と設定はお客様が行います。 そのため、お客様は削除ロックを削除してから、SSIS IR を再度停止する必要があります。

### <a name="nodeunavailable"></a>NodeUnavailable

このエラーは IR の実行中に発生し、IR が異常になったことを意味します。 このエラーは常に、SSIS IR が必要なサービスに接続できないようにする DNS サーバーまたは NSG 構成の変更によって発生します。 DNS サーバーと NSG の構成はお客様が制御するため、お客様は自分の側の障害となっている問題を修正する必要があります。 詳細については、[SSIS IR 仮想ネットワークの構成](./join-azure-ssis-integration-runtime-virtual-network.md)に関する記事を参照してください。 問題が解決しない場合は、Azure Data Factory サポート チームにお問い合わせください。

## <a name="static-public-ip-addresses-configuration"></a>静的パブリック IP アドレスの構成

Azure-SSIS IR を Azure Virtual Network に参加させる場合、特定の IP アドレスのみにアクセスが制限されているデータ ソースに IR からアクセスできるように、IR 用に独自の静的パブリック IP アドレスを利用することもできます。 詳細については、「[Azure-SSIS Integration Runtime を仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

上記の仮想ネットワークの問題に加えて、静的パブリック IP アドレス関連の問題も発生することがあります。 解決策については、次のエラーを確認してください。

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

このエラーは、Azure-SSIS IR の開始時にさまざまな理由で発生する可能性があります。

| エラー メッセージ | 解決策|
|:--- |:--- |
| The provided static public IP address is already used, please provide two unused ones for your Azure-SSIS Integration Runtime. (指定された静的パブリック IP アドレスは既に使用されています。Azure-SSIS Integration Runtime 向けに未使用の IP アドレスを 2 つ指定してください。) | 未使用の静的パブリック IP アドレスを 2 つ選択するか、指定したパブリック IP アドレスへの現在の参照を削除してから、Azure-SSIS IR を再起動してください。 |
| The provided static public IP address has no DNS name, please provide two of them with DNS name for your Azure-SSIS Integration Runtime. (指定された静的パブリック IP アドレスには DNS 名が設定されていません。Azure-SSIS Integration Runtime 向けに、これら 2 つに DNS 名を指定してください。) | 下図に示すように、Azure portal でパブリック IP アドレスの DNS 名を設定できます。 具体的な手順は次のとおりです。(1) Azure portal を開き、目的のパブリック IP アドレスのリソース ページに移動します。(2) **[構成]** セクションを選択して DNS 名を設定し、 **[保存]** ボタンをクリックします。(3) Azure-SSIS IR を再起動します。 |
| The provided VNet and static public IP addresses for your Azure-SSIS Integration Runtime must be in the same location. (Azure-SSIS Integration Runtime に指定する VNet と静的パブリック IP アドレスは、同じ場所内に存在する必要があります。) | Azure ネットワークの要件に従い、静的パブリック IP アドレスと仮想ネットワークは、同じ場所およびサブスクリプション内に存在する必要があります。 有効な静的パブリック IP アドレスを 2 つ指定し、Azure-SSIS IR を再起動してください。 |
| The provided static public IP address is a basic one, please provide two standard ones for your Azure-SSIS Integration Runtime. (指定された静的パブリック IP アドレスは Basic のものです。Azure-SSIS Integration Runtime では Standard の IP アドレスを 2 つ指定してください。) | 詳細については、[パブリック IP アドレスの SKU](../virtual-network/public-ip-addresses.md#sku) に関するセクションを参照してください。 |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Azure-SSIS IR のプロビジョニングに失敗した場合、作成されたすべてのリソースが削除されます。 ただし、サブスクリプション レベルまたは (静的パブリック IP アドレスが含まれる) リソース グループ レベルのリソース削除ロックがある場合、ネットワーク リソースは想定どおりに削除されません。 エラーを修正するには、削除ロックを解除し、IR を再起動してください。

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Azure-SSIS IR を停止すると、パブリック IP アドレスが含まれるリソース グループ内に作成されているネットワーク リソースがすべて削除されます。 ただし、サブスクリプション レベルまたは (静的パブリック IP アドレスが含まれる) リソース グループ レベルのリソース削除ロックがある場合、削除は失敗することがあります。 削除ロックを解除し、IR を再起動してください。

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR は、定期的に自動更新されます。 アップグレード中に新しい IR ノードが作成され、古いノードは削除されます。 また、古いノードで作成済みのネットワーク リソース (ロード バランサー、ネットワーク セキュリティ グループなど) が削除され、自分のサブスクリプション下に新しいネットワーク リソースが作成されます。 このエラーは、サブスクリプション レベルまたは (静的パブリック IP アドレスが含まれる) リソース グループ レベルのリソース削除ロックがあることが原因で、古いノードのネットワーク リソースを削除できなかったことを意味します。 古いノードをクリーンアップしてこれらのノード用の静的パブリック IP アドレスを解放できるように、削除ロックを解除してください。 そうしないと、静的パブリック IP アドレスを解放できないので、IR のアップグレードを進めることができません。

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

独自の静的パブリック IP アドレスを使用する場合、パブリック IP アドレスを 2 つ指定する必要があります。 これらのうち 1 つは直ちに IR ノードの作成に使用され、もう 1 つは IR のアップグレード中に使用されます。 このエラーは、アップグレード中に後者のパブリック IP アドレスを使用できないときに発生する可能性があります。 考えられる原因については、「[InvalidPublicIPSpecified](#InvalidPublicIPSpecified)」を参照してください。