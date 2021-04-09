---
title: SQL Server バージョンのインプレース変更
description: Azure で SQL Server 仮想マシンのバージョンを変更する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358845"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Azure VM 上の SQL Server バージョンのインプレース変更

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Microsoft Azure の Windows 仮想マシン (VM) 上で Microsoft SQL Server のバージョンを変更する方法について説明します。

## <a name="prerequisites"></a>前提条件

SQL Server のインプレース アップグレードを実行するには、次の条件が適用されます。

- SQL Server の目的のバージョンのセットアップ メディアが必要です。 [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、SQL Server の新しいバージョン (通常、C:\SQLServerFull にあります) を含む Azure Marketplace の SQL Server VM イメージから、セットアップ メディアを使用できます。
- エディションのアップグレードは、[サポート アップグレード パス](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)に従う必要があります。

## <a name="planning-for-version-change"></a>バージョンの変更の計画

バージョンを変更する前に、次の項目を確認することをお勧めします。

1. アップグレードを計画しているバージョンの新機能を確認します。

   - [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15) の新機能
   - [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017) の新機能
   - [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016) の新機能


1. アップグレードの影響を最小限に抑えるため、データベース互換性モードを使用できるように、変更するバージョンの[互換性証明書](/sql/database-engine/install-windows/compatibility-certification)を確認することをお勧めします。
1. 正常な結果を得るには、次の記事を参照してください。

   - [ビデオ: Modernizing SQL Server | Pam Lahoud & Pedro Lopes | 20 Years of PASS (SQL Server を最新にする | Pam Lahoud & Pedro Lopes | 20 年来の PASS)](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [AB テスト用の Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)
   - [クエリ調整アシスタントを使用したデータベースのアップグレード](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [データベース互換性レベルの変更とクエリ ストアの使用](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>SQL バージョンのアップグレード

> [!WARNING]
> SQL Server のバージョンをアップグレードすると、Analysis Services や R Services などのすべての関連するサービスに加えて、SQL Server のサービスが再起動されます。

SQL Server のバージョンをアップグレードするには、SQL Server の[アップグレード パスをサポートする](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)新しいバージョンの SQL Server セットアップ メディアを入手し、次の手順を実行します。

1. プロセスを開始する前に、システム データベース (tempdb を除く) とユーザー データベースを含むデータベースをバックアップします。 Azure Backup Services を使用して、アプリケーション整合性のある VM レベルのバックアップを作成することもできます。
1. SQL Server のインストール メディアから Setup.exe を開始します。
1. インストール ウィザードにより SQL Server インストール センターが起動されます。 SQL Server の既存のインスタンスをアップグレードするには、ナビゲーション ウィンドウで **[インストール]** を選択し、 **[Upgrade from an earlier version of SQL Server]\(以前のバージョンの SQL Server からのアップグレード\)** を選択します。

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="SQL Server のバージョンをアップグレードするための選択":::

1. **[プロダクト キー]** ページで、SQL Server の無償のエディションにアップグレードするか、製品の運用バージョンの PID キーを持っているかを示すオプションを選択します。 詳細については、「[SQL Server 2019 (15.x) のエディションとサポートされる機能](/sql/sql-server/editions-and-components-of-sql-server-version-15)」と「[サポートされているバージョンとエディションのアップグレード (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades)」を参照してください。
1. **[アップグレードの準備完了]** ページが表示されるまで **[次へ]** を選択し、 **[アップグレード]** を選択します。 変更が有効になるまで数分間、セットアップ ウィンドウの応答が停止することがあります。 **[完了]** ページで、アップグレードが完了したことが確認されます。 アップグレード手順の詳細については、[完全な手順](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure)を参照してください。

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="[完了] ページ":::

バージョンを変更するだけでなく SQL Server エディションを変更した場合は、エディションも更新します。SQL VM インスタンスを変更するには、「**ポータルでバージョンとエディションを確認する**」セクションを参照してください。

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="バージョン メタデータの変更":::

## <a name="downgrade-the-version-of-sql-server"></a>SQL Server バージョンのダウングレード

SQL Server のバージョンをダウングレードするには、SQL Server を完全にアンインストールし、目的のバージョンを使用して再インストールする必要があります。 これは、以前のデータベースを、新しいバージョンから新しくインストールされた以前のバージョンに復元できないため、SQL Server の新規インストールに似ています。 データベースを最初から再作成する必要があります。 アップグレード中に SQL Server のエディションも変更した場合は、Azure portal で SQL Server VM の **[エディション]** プロパティを新しいエディションの値に変更します。 これにより、この VM に関連付けられているメタデータと請求先が更新されます。

> [!WARNING]
> SQL Server のインプレース ダウングレードはサポートされていません。

次の手順に従って、SQL Server のバージョンをダウングレードできます。

1. [新しいバージョンでのみ使用できる機能](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)を使用していないことを確認します。
1. システム データベース (tempdb を除く) とユーザー データベースを含む、すべてのデータベースをバックアップします。
1. 必要なすべてのサーバーレベルのオブジェクト (サーバー トリガー、ロール、ログイン、リンク サーバー、ジョブ、資格情報、証明書など) をエクスポートします。
1. 以前のバージョンのユーザー データベースを再作成するスクリプトがない場合は、BCP.exe、SSIS、または DACPAC を使用して、すべてのオブジェクトをスクリプト化し、すべてのデータをエクスポートする必要があります。

   ターゲット バージョン、依存オブジェクト、詳細オプションなどの項目をスクリプト化する場合は、適切なオプションを選択していることを確認してください。

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="スクリプト作成オプション":::

1. SQL Server および関連付けられているすべてのサービスを、完全にアンインストールします。
1. VM を再起動します。
1. プログラムの目的のバージョンのメディアを使用して SQL Server をインストールします。
1. 最新のサービス パックと累積的な更新プログラムをインストールします。
1. 必要なすべてのサーバーレベルのオブジェクト (手順 3 でエクスポートしたもの) をインポートします。
1. (作成したスクリプトまたは手順 4 のファイルを使用して) 必要なすべてのユーザー データベースを最初から再作成します。

## <a name="verify-the-version-and-edition-in-the-portal"></a>ポータルでバージョンとエディションを確認する

SQL Server のバージョンを変更した後、Azure portal を使用して SQL Server のバージョンを表示できるように、SQL Server VM を [SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に再び登録します。 一覧表示されたバージョン番号には、SQL Server インストールの新しくアップグレードされたバージョンとエディションが反映されます。

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="バージョンの確認":::

> [!NOTE]
> SQL IaaS Agent 拡張機能に既に登録している場合は、[RP から登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)してから、[SQL VM リソースを再度登録](sql-agent-extension-manually-register-single-vm.md#register-with-extension)して、VM にインストールされている SQL Server の正しいバージョンとエディションを検出できるようにします。 これにより、この VM に関連付けられているメタデータと請求先情報が更新されます。

## <a name="remarks"></a>解説

- アップグレードが完了したら、バックアップの開始、統計の更新、インデックスの再構築、整合性の確認を行うことをお勧めします。 個々のデータベース互換性レベルをチェックして、目的のレベルが反映されていることを確認することもできます。
- VM で SQL Server が更新されたら、Azure portal 内の SQL Server の **[エディション]** プロパティが、課金対象のインストールされているエディション番号と一致していることを確認します。
- [エディションを変更する](change-sql-server-edition.md#change-edition-in-portal)機能は、SQL IaaS Agent 拡張機能の機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM が拡張機能に自動的に登録されます。 ただし、SQL Server を自分でインストールするお客様は、手動で [SQL Server VM を登録](sql-agent-extension-manually-register-single-vm.md)する必要があります。
- SQL Server VM リソースを削除すると、イメージのハード コーディングされたエディション設定が復元されます。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
- [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
- [Windows VM 上の SQL Server のリリース ノート](doc-changes-updates-release-notes.md)