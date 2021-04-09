---
title: Azure AD Connect:DirSync からのアップグレード | Microsoft Docs
description: DirSync から Azure AD Connect にアップグレードする方法について説明します。 この記事では、DirSync から Azure AD Connect へのアップグレード手順について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996527"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect:DirSync からのアップグレード
Azure AD Connect は DirSync の後継のツールです。 このトピックでは、DirSync からアップグレードする方法について説明します。 以下の手順は、Azure AD Connect の別のリリースまたは Azure AD Sync からのアップグレードには使用できません。

Azure AD Connect のインストールを始める前に、必ず [Azure AD Connect をダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)し、[Azure AD Connect のハードウェアと前提条件](how-to-connect-install-prerequisites.md)に関するページに記載されている前提条件の手順を完了してください。 特に、以下の領域は DirSync とは異なるため、確認が必要です。

* .NET と PowerShell の必須バージョン。 DirSync での必須バージョンよりも新しいバージョンがサーバーにインストールされている必要があります。
* プロキシ サーバーの構成。 プロキシ サーバーを使用してインターネットに接続する場合は、アップグレードする前にこの設定を構成する必要があります。 DirSync では、インストールしたユーザー向けに構成されたプロキシ サーバーが常に使用されていましたが、Azure AD Connect では、コンピューターの設定が使用されます。
* プロキシ サーバーで開く必要がある URL。 基本的なシナリオでは、これらのシナリオは DirSync でもサポートされるため、要件は同じです。 Azure AD Connect で導入された新機能のいずれかを使用する場合は、いくつかの新しい URL を開く必要があります。

> [!NOTE]
> Azure AD に対する変更の同期を新しい Azure AD Connect サーバーが開始できるようにした後は、DirSync または Azure AD Sync を使用してロールバックしないでください。Azure AD Connect から DirSync、Azure AD Sync などの従来のクライアントへのダウングレードはサポートされておらず、Azure AD のデータ損失などの問題につながる場合があります。

DirSync からアップグレードしない場合は、関連ドキュメントでその他のシナリオを確認してください。

## <a name="upgrade-from-dirsync"></a>DirSync からのアップグレード
現在の DirSync のデプロイに応じて、アップグレードにはさまざまなオプションがあります。 予想されるアップグレード時間が 3 時間未満の場合は、インプレース アップグレードを実行することをお勧めします。 予想されるアップグレード時間が 3 時間を超える場合は、別のサーバーで並列デプロイを行うことをお勧めします。 オブジェクトの数が 50,000 を超える場合は、アップグレードに要する時間が 3 時間を超えることが予想されます。

| シナリオ |
| --- |
| [インプレース アップグレード](#in-place-upgrade) |
| [並列デプロイ](#parallel-deployment) |

> [!NOTE]
> DirSync から Azure AD Connect へのアップグレードを計画している場合は、アップグレードより前に DirSync を自分でアンインストールしないでください。 Azure AD Connect が DirSync から構成を読み取って移行し、サーバーを検査した後に、アンインストールします。

**インプレース アップグレード**  
アップグレードが完了する予定時刻がウィザードに表示されます。 この推定値は、50,000 のオブジェクト (ユーザー、連絡先、グループ) を含むデータベースのアップグレードが完了するまでに 3 時間かかるという前提に基づいています。 データベース内のオブジェクトの数が 50,000 未満である場合は、Azure AD Connect ではインプレース アップグレードが推奨されています。 続行すると、現在の設定がアップグレード中に自動的に適用され、サーバーが自動的にアクティブな同期を再開します。

構成の移行を実行し、並列デプロイを実行する場合は、インプレース アップグレードに関する推奨事項をオーバーライドしてもかまいません。 たとえば、ハードウェアとオペレーティング システムを更新することができます。 詳細については、「[並列デプロイ](#parallel-deployment)」セクションを参照してください。

**並列デプロイ**  
オブジェクトの数が 50,000 を超える場合は、並列デプロイをお勧めします。 このデプロイでは、ユーザーに対して操作時の遅延が発生しません。 Azure AD Connect のインストールでは、アップグレードのためのダウンタイムを予測しますが、過去に DirSync をアップグレードしたことがある場合は、その経験が最善の指標となります。

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>アップグレード対象の、サポートされている DirSync の構成
アップグレードされた DirSync では、次の構成の変更がサポートされています。

* ドメインと OU のフィルター処理
* 別の ID (UPN)
* パスワード同期と Exchange ハイブリッドの設定
* フォレスト/ドメインと Azure AD の設定
* ユーザー属性に基づくフィルター処理

次の変更をアップグレードすることはできません。 これが構成されている場合は、アップグレードがブロックされます。

* サポートされていない DirSync の変更 (属性の削除やカスタム拡張 DLL の使用など)

![アップグレードのブロック](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

そのような場合、[ステージング モード](how-to-connect-sync-staging-server.md)で新しい Azure AD Connect サーバーをインストールし、古い DirSync と新しい Azure AD Connect の構成を確認することが推奨されます。 カスタム構成を使用して変更をもう一度適用する場合は、[Azure AD Connect Sync のカスタム構成](how-to-connect-sync-whatis.md)に関するページを参照してください。

DirSync がサービス アカウントで使用したパスワードは取得できず、移行されません。 これらのパスワードはアップグレード中にリセットされます。

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>DirSync から Azure AD Connect へのアップグレードの大まかな手順
1. Azure AD Connect へようこそ
2. 現在の DirSync 構成の分析
3. Azure AD のグローバル管理者のパスワードの収集
4. エンタープライズ管理者アカウントの資格情報の収集 (Azure AD Connect のインストール時にのみ使用)
5. Azure AD Connect のインストール
   * DirSync のアンインストール (または一時的な無効化)
   * Azure AD Connect のインストール
   * 必要に応じて、同期を開始します。

次の場合、追加の手順が必要になります。

* 完全バージョンの SQL Server を現在使用している場合 - ローカルまたはリモート
* 同期するオブジェクトが 50,000 以上ある場合

## <a name="in-place-upgrade"></a>一括アップグレード
1. Azure AD Connect インストーラー (MSI) を起動します。
2. ライセンス条項とプライバシーに関する声明を確認し、同意します。  
   ![Azure AD へようこそ](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. [次へ] をクリックして、既存の DirSync インストールを分析します。  
   ![既存のディレクトリ同期のインストールの分析](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. 分析が完了すると、続行方法に関する推奨事項が提示されます。  
   * SQL Server Express を使用しており、オブジェクトの数が 50,000 未満である場合は、次の画面が表示されます。  
     ![分析が完了し、DirSync からアップグレードする準備が整いました。](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * DirSync に完全バージョンの SQL Server を使用する場合、次のページが表示されます。  
     ![使用されている既存の SQL データベース サーバーを示すスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     DirSync によって使用されている既存の SQL Server データベース サーバーに関する情報が表示されます。 必要に応じて、適切に調整を行います。 **[次へ]** をクリックしてインストールを続行します。
   * 50,000 個を超えるオブジェクトがある場合は、代わりにこの画面が表示されます。  
     ![アップグレードするオブジェクトが 50,000 個を超える場合に表示されるスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     インプレース アップグレードを続行するには、"**このコンピューターの DirSync のアップグレードを続行します。** " の横のチェック ボックスをオンにします。
     代わりに[並列デプロイ](#parallel-deployment)を行うには、DirSync の構成設定をエクスポートして新しいサーバーに移します。
5. Azure AD への接続に現在使用しているアカウントのパスワードを入力します。 これは、DirSync によって現在使用されているアカウントでなければなりません。  
   ![Azure ADの資格情報を入力します。](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   接続に問題があり、エラーが発生する場合は、[接続の問題に対するトラブルシューティング](tshoot-connect-connectivity.md)についてのページを参照してください。
6. Active Directory のエンタープライズ管理者アカウントを指定します。  
   ![ADDS の資格情報を入力する](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. 構成する準備が整いました。 **[アップグレード]** をクリックすると、DirSync がアンインストールされ、Azure AD Connect が構成されて、同期が開始されます。  
   ![構成の準備完了](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. インストールが完了した後、Synchronization Service Manager または同期規則エディターを使用する前に、サインアウトしてもう一度 Windows にサインインするか、他の構成の変更を試します。

## <a name="parallel-deployment"></a>並列デプロイ
### <a name="export-the-dirsync-configuration"></a>DirSync の構成をエクスポートする
**並列デプロイメント - 50,000 以上のオブジェクト**

オブジェクトの数が 50,000 を超える場合、Azure AD Connect のインストールでは並列デプロイが推奨されます。

次のような画面が表示されます。  
![分析完了](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

並列デプロイを開始する場合は、次の手順を実行する必要があります。

* **[設定のエクスポート]** をクリックします。 Azure AD Connect を別のサーバーにインストールすると、これらの設定が現在の DirSync から新しい Azure AD Connect のインストールに移行されます。

設定が正常にエクスポートされたら、DirSync サーバーで、Azure AD Connect ウィザードを終了できます。 次の手順に進み、別のサーバーに Azure AD Connect をインストールします。

**並列デプロイメント - 50,000 未満のオブジェクト**

オブジェクトの数が 50,000 未満の場合に並列デプロイメントを実行するには、次の手順を実行します。

1. Azure AD Connect インストーラー (MSI) を実行します。
2. **[Azure AD Connect へようこそ]** 画面が表示されたら、ウィンドウの右上隅にある [X] をクリックしてインストール ウィザードを終了します。
3. コマンド プロンプトを開きます。
4. Azure AD Connect のインストール場所 (既定: C:\Program Files\Microsoft Azure Active Directory Connect) から、次のコマンドを実行します。`AzureADConnect.exe /ForceExport`
5. **[設定のエクスポート]** をクリックします。 Azure AD Connect を別のサーバーにインストールすると、これらの設定が現在の DirSync から新しい Azure AD Connect のインストールに移行されます。

![新しい Azure AD Connect のインストールに設定を移行するための [設定のエクスポート] オプションを示すスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

設定が正常にエクスポートされたら、DirSync サーバーで、Azure AD Connect ウィザードを終了できます。 次の手順に進み、別のサーバーに Azure AD Connect をインストールします。

### <a name="install-azure-ad-connect-on-separate-server"></a>別のサーバーに Azure AD Connect をインストールする
Azure AD Connect を新しいサーバーにインストールする場合、Azure AD Connect のクリーン インストールを実行するものと見なされます。 DirSync の構成を使用する必要があるため、実行する手順が増えます。

1. Azure AD Connect インストーラー (MSI) を実行します。
2. **[Azure AD Connect へようこそ]** 画面が表示されたら、ウィンドウの右上隅にある [X] をクリックしてインストール ウィザードを終了します。
3. コマンド プロンプトを開きます。
4. Azure AD Connect のインストール場所 (既定: C:\Program Files\Microsoft Azure Active Directory Connect) から、次のコマンドを実行します。`AzureADConnect.exe /migrate`
   Azure AD Connect のインストール ウィザードが起動し、次の画面が表示されます。  
   ![アップグレード時に設定ファイルをインポートする場所を示すスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. DirSync インストールからエクスポートされた設定ファイルを選択します。
6. 次の高度なオプションを構成します。
   * Azure AD Connect のカスタムのインストール場所。
   * SQL Server の既存のインスタンス (既定: Azure AD Connect では、SQL Server 2012 Express がインストールされます)。 DirSync サーバーと同じデータベース インスタンスは使用しないでください。
   * SQL Server への接続に使用するサービス アカウント (SQL Server データベースがリモートの場合、このアカウントはドメイン サービス アカウントにする必要があります)。
     これらのオプションは、次の画面で表示されます。  
     ![DirSync からアップグレードするための高度な構成オプションを示すスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. **[次へ]** をクリックします。
8. **[構成の準備完了]** ページで **[構成が完了したら、同期処理を開始してください。]** チェック ボックスをオンのままにします。 サーバーは[ステージング モード](how-to-connect-sync-staging-server.md)になっているため、変更は Azure AD にエクスポートされません。
9. **[インストール]** をクリックします。
10. インストールが完了した後、Synchronization Service Manager または同期規則エディターを使用する前に、サインアウトしてもう一度 Windows にサインインするか、他の構成の変更を試します。

> [!NOTE]
> Windows Server Active Directory と Azure Active Directory の間で同期が開始されますが、変更は Azure AD にエクスポートされません。 一度にアクティブにし変更をエクスポートできる同期ツールは 1 つだけです。 この状態は[ステージング モード](how-to-connect-sync-staging-server.md)と呼ばれます。

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Azure AD Connect の同期を開始する準備が完了していることを確認する
Azure AD Connect で DirSync からの引き継ぎの準備ができていることを確認するために、[スタート] メニューから **[Azure AD Connect]** グループの **Synchronization Service Manager** を開く必要があります。

アプリケーションで、 **[操作]** タブに移動します。このタブで、次の操作が完了していることを確認します。

* AD コネクタへのインポート
* Azure AD コネクタへのインポート
* AD コネクタへの完全な同期
* Azure AD コネクタへの完全な同期

![インポートと同期の完了](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

これらの操作の結果を確認し、エラーが発生しないことを確認します。

どの変更が Azure AD にエクスポートされるのかを調べるには、[ステージング モード](how-to-connect-sync-staging-server.md)で構成を確認する方法をお読みください。 予期しない内容が表示されなくなるまで構成の変更を行ってください。

これらの手順が完了し、結果に問題がなければ、DirSync から Azure AD に切り替える準備ができています。

### <a name="uninstall-dirsync-old-server"></a>DirSync (古いサーバー) をアンインストールする
* **[プログラムと機能]** で **[Windows Azure Active Directory 同期ツール]** を見つけます。
* **[Windows Azure Active Directory 同期ツール]**
* アンインストールが完了するまで最大で 15 分かかる場合があります。

後で DirSync をアンインストールする場合は、一時的にサーバーをシャットダウンするか、サービスを無効にしておくこともできます。 問題が生じた場合は、この方法で再び有効にすることができます。 ただし、次の手順は失敗しないものと思われますので、必ずしも必要になるとは限りません。

DirSync がアンインストールされているか、無効になっている場合は、Azure AD へのエクスポートが行われるアクティブなサーバーは存在しません。 オンプレミスの Active Directory のすべての変更が引き続き Azure AD に同期されるようにするには、次の手順を完了して Azure AD Connect を有効にする必要があります。

### <a name="enable-azure-ad-connect-new-server"></a>Azure AD Connect (新しいサーバー) を有効化する
インストール後に Azure AD Connect をもう一度開くと、追加の構成変更ができるようになります。 [スタート] メニューから、またはデスクトップのショートカットから **[Azure AD Connect]** を開始します。 MSI のインストールをもう一度実行しようとしないでください。

次のように表示されます。  
![追加のタスク](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* **[ステージング モードの構成]** を選択します。
* **[ステージング モードを有効にする]** チェックボックスをオフにして、ステージングを停止します。

![ステージング モードを有効にするためのオプションを示すスクリーンショット。](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* **[次へ]** をクリックします。
* [確認] ページで **[インストール]** ボタンをクリックします。

これで Azure AD Connect がアクティブなサーバーとなったため、今後は既存の DirSync サーバーは使用しないでください。

## <a name="next-steps"></a>次のステップ
Azure AD Connect がインストールされたので、[インストールを確認し、ライセンスを割り当てる](how-to-connect-post-installation.md)ことができます。

インストールの結果有効になった新しい機能について詳しくは、[自動アップグレード](how-to-connect-install-automatic-upgrade.md)、[誤った削除操作を防止する機能](how-to-connect-sync-feature-prevent-accidental-deletes.md)、[Azure AD Connect Health](how-to-connect-health-sync.md) に関する各ページを参照してください。

一般的なトピックについては、[スケジューラの使用と同期のトリガー方法](how-to-connect-sync-feature-scheduler.md)に関するページを参照してください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
