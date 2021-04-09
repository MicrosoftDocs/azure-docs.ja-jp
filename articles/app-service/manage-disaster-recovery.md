---
title: リージョン全体の障害から復旧する
description: Azure App Service によって、ビジネス継続性とディザスター リカバリー (BCDR) の機能を維持する方法について説明します。 Azure のリージョン全体の障害からアプリを復旧します。
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073747"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service アプリを別のリージョンに移動する

この記事では、Azure リージョン全体に影響を与える障害が発生したときに、App Service リソースを別の Azure リージョンでオンラインに戻す方法について説明します。 障害によって Azure リージョン全体がオフラインになると、そのリージョンでホストされているすべての App Service アプリがディザスター リカバリー モードになります。 アプリを別のリージョンに復元したり、影響を受けたアプリからファイルを回復したりするのに役立つ機能が用意されています。

App Service リソースはリージョン固有のものであり、リージョン間で移動することはできません。 アプリを別のリージョンの新しいアプリに復元し、ミラーリング構成または新しいアプリのリソースを作成する必要があります。

## <a name="prerequisites"></a>前提条件

- [なし] : [スナップショットからの復元](app-service-web-restore-snapshots.md)には通常は **Premium** レベルが必要ですが、ディザスター リカバリー モードでは、影響を受けたアプリのレベルに関係なく、影響を受けたアプリに対してこのレベルが自動的に有効になります。

## <a name="prepare"></a>準備

影響を受けたアプリが現在使用しているすべての App Service リソースを特定します。 次に例を示します。

- App Service アプリ
- [App Service プラン](overview-hosting-plans.md)
- [デプロイ スロット](deploy-staging-slots.md)
- [Azure で購入したカスタム ドメイン](manage-custom-dns-buy-domain.md)
- [SSL 証明書](configure-ssl-certificate.md)
- [Azure Virtual Network の統合](web-sites-integrate-with-vnet.md)
- [ハイブリッド接続](app-service-hybrid-connections.md)。
- [マネージド ID](overview-managed-identity.md)
- [バックアップの設定](manage-backup.md)

インポートされた証明書やハイブリッド接続などの特定のリソースには、他の Azure サービスとの統合が含まれています。 これらのリソースをリージョン間で移動する方法については、それぞれのサービスのドキュメントを参照してください。

## <a name="restore-app-to-a-different-region"></a>アプリを別のリージョンに復元する

1. 影響を受けたアプリとは *異なる* Azure リージョンで、新しい App Service アプリを作成します。 これはディザスター リカバリー シナリオにおけるターゲットアプリです。

1. [Azure portal](https://portal.azure.com) で、影響を受けたアプリの管理ページに移動します。 障害が発生した Azure リージョンでは、影響を受けたアプリに警告テキストが表示されます。 警告テキストをクリックします。

    ![影響を受けたアプリのページのスクリーンショット。 状況について説明し、アプリ復元用のリンクが記載された警告通知が表示されます。](media/manage-disaster-recovery/restore-start.png)

1. **[バックアップの復元]** ページで、次の表に従って復元操作を構成します。 完了したら、 **[OK]** をクリックします。

   | 設定 | 値 | 説明 |
   |-|-|-|
   | **スナップショット (プレビュー)** | スナップショットを選択します。 | 2 つの最新のスナップショットを使用できます。 |
   | **復元先** | **既存のアプリ** | **[Click here to change the restore destination app]\(復元先のアプリを変更するにはここをクリックします\)** という下の注意をクリックし、ターゲットアプリを選択します。 ディザスター シナリオでは、別の Azure リージョンのアプリにのみスナップショットを復元できます。 |
   | **サイト構成の復元** | **はい** | |

    ![[バックアップの復元] ページのスクリーンショット。 特定のスナップショット、前の表の一覧に示されたオプション、および [OK] ボタンが強調表示されています。](media/manage-disaster-recovery/restore-configure.png)

3. 影響を受けたアプリをミラーリングするようにターゲット アプリ内の[他のすべて](#prepare)を構成し、構成を確認します。

4. カスタム ドメインでターゲット アプリをポイントする準備ができたら、[ドメイン名を再マッピング](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)します。

## <a name="recover-app-content-only"></a>アプリ コンテンツのみを回復する

影響を受けたアプリを復元せずにファイルを回復するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、影響を受けたアプリの管理ページに移動し、 **[発行プロファイルの取得]** をクリックします。

    ![影響を受けたアプリのページのスクリーンショット。 警告通知は表示されますが、強調表示はされません。 その代わり、[発行プロファイルの取得] 項目が強調表示されています。](media/manage-disaster-recovery/get-publish-profile.png)

1. ダウンロードしたファイルを開き、名前に `ReadOnly - FTP` を含む発行プロファイルを見つけます。 これがディザスター リカバリー プロファイルです。 次に例を示します。

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    3 つの属性値をコピーします。 
        
    - `publishUrl`: FTP ホスト名
    - `userName` および `userPWD`: FTP 資格情報

1. 任意の FTP クライアントを使用して、ホスト名と資格情報を使用して、影響を受けたアプリの FTP ホストに接続します。

1. 接続したら、 */site/wwwroot* フォルダー全体をダウンロードします。 次のスクリーンショットは、[FileZilla](https://filezilla-project.org/) でダウンロードする方法を示しています。

    ![FileZilla のファイル階層のスクリーンショット。 wwwroot フォルダーが強調表示されていて、ショートカット メニューが表示されています。 そのメニューの [ダウンロード] が強調表示されています。](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>次のステップ
[Azure でスナップショットからアプリケーションを復元する](app-service-web-restore-snapshots.md)
