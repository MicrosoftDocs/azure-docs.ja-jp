---
title: GPO を使用して IE 用 Azure アクセス パネル拡張機能をデプロイする | Microsoft Docs
description: グループ ポリシーを使用してマイ アプリ ポータル用の Internet Explorer アドオンをデプロイする方法。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf665362e2d20f26c17e8a4ae9da29fc30cb47ce
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481286"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>方法:グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする

このチュートリアルでは、グループ ポリシーを使用して Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールする方法を示します。 この拡張機能は、 [パスワード ベースのシングル サインオン](what-is-single-sign-on.md#password-based-sso)を使用するように構成されているアプリにサインインする必要がある Internet Explorer ユーザーに必要なものです。

管理者にはこの拡張機能のデプロイメントを自動化することをお勧めします。 自動化しないと、ユーザーは自分で拡張機能をダウンロードしてインストールする必要があるので、ユーザー エラーが発生しやすく、管理者のアクセス許可が必要になります。 このチュートリアルでは、グループ ポリシーを使用してソフトウェアのデプロイメントを自動化する方法について説明します。 [グループ ポリシーの詳細についてはこちらを参照してください。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

アクセス パネルの拡張機能は [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) および [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) でも使用でき、どちらの場合もインストールに管理者のアクセス許可は必要ありません。

## <a name="prerequisites"></a>前提条件

* [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。
* グループ ポリシー オブジェクト (GPO) を編集するには、"設定の編集" アクセス許可が必要です。 既定でこのアクセス許可を持っているセキュリティ グループのメンバーはDomain Administrators、Enterprise Administrators、Group Policy Creator Owners です。 [詳細情報。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>手順 1:配布ポイントを作成する

最初に、拡張機能をリモートでインストールするコンピューターがアクセスできるネットワークの場所に、インストーラー パッケージを置く必要があります。 そのためには、次の手順に従います。

1. サーバーに管理者としてサインオンします。
1. **[サーバー マネージャー]** ウィンドウで、 **[ファイルおよび記憶域サービス]** に移動します。

    ![[ファイル サービスおよびストレージ サービス] を開く](./media/deploy-access-panel-browser-extension/files-services.png)
1. **[共有]** タブに移動します。 **[タスク]**  >  **[新しい共有...]** の順にクリックします

    ![[ファイル サービスおよびストレージ サービス] を開く](./media/deploy-access-panel-browser-extension/shares.png)
1. **[新しい共有ウィザード]** を完了して、ユーザーのコンピューターからアクセスできるようにアクセス許可を設定します。 [共有の詳細についてはこちらを参照してください。](https://technet.microsoft.com/library/cc753175.aspx)
1. 次の Microsoft Windows インストーラー パッケージ (.msi ファイル) をダウンロードします。[Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. インストーラー パッケージを共有上の目的の場所にコピーします。

    ![.msi ファイルを共有にコピーする](./media/deploy-access-panel-browser-extension/copy-package.png)

1. クライアント コンピューターが共有のインストーラー パッケージにアクセスできることを確認します。

## <a name="step-2-create-the-group-policy-object"></a>手順 2:グループ ポリシー オブジェクトを作成する

1. Active Directory Domain Services (AD DS) のインストールをホストしているサーバーにサインインします。
1. サーバー マネージャーで、 **[ツール]**  >  **[グループ ポリシーの管理]** に移動します。

    ![[ツール] > [グループ ポリシーの管理] に移動する](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. **[グループ ポリシーの管理** ] の左側のウィンドウで組織単位 (OU) 階層を表示し、グループ ポリシーを適用するスコープを決定します。 たとえば、テストのために数ユーザーにデプロイする場合は小さい OU を選択し、組織全体にデプロイする場合は最上位レベルの OU を選択します。

   > [!NOTE]
   > 組織単位 (OU) を作成または編集する場合は、サーバー マネージャーに戻り、 **[ツール]**  >  **[Active Directory ユーザーとコンピューター]** に移動します。

1. OU を選択して右クリックし、 **[このドメインに GPO を作成し、このコンテナーにリンクする...]** を選択します

    ![新しい GPO の作成オプションを示すスクリーンショット](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. **[新しい GPO]** プロンプトで、新しいグループ ポリシー オブジェクトの名前を入力します。
1. 作成したグループ ポリシー オブジェクトを右クリックし、 **[編集]** を選択します。

## <a name="step-3-assign-the-installation-package"></a>手順 3:インストール パッケージを割り当てる

1. **[コンピューターの構成]** と **[ユーザーの構成]** のどちらを基にして拡張機能をデプロイするかを決めます。 [コンピューターの構成](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)を使用すると、コンピューターにログオンしているユーザーに関係なく、拡張機能はコンピューターにインストールされます。 [ユーザーの構成](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)では、ユーザーがログオンしているコンピューターに関係なく、拡張機能はユーザーに対してインストールされます。
1. **[グループ ポリシー管理エディター]** の左側のウィンドウで、選択した構成の種類に応じて、次のどちらかのフォルダー パスに移動します。

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
1. **[ソフトウェアのインストール]** を右クリックして、 **[新規]**  >  **[パッケージ...]** の順に選択します
1. 「[手順 1:配布ポイントを作成する](#step-1-create-the-distribution-point)」でインストーラー パッケージを含む共有フォルダーに移動し、.msi ファイルを選択して、 **[開く]** をクリックします。

   > [!IMPORTANT]
   > 共有が同じサーバー上に存在する場合は、ローカル ファイル パスではなく、ネットワーク ファイル パスから .msi にアクセスしていることを確認します。

    ![共有フォルダーからインストール パッケージを選択する](./media/deploy-access-panel-browser-extension/select-package.png)

1. **[ソフトウェアの展開]** プロンプトで、デプロイ方法として **[割り当て]** を選択します。 次に、 **[OK]** をクリックします

選択した OU に拡張機能がデプロイされます。 [グループ ポリシー ソフトウェアのインストールの詳細についてはこちらを参照してください。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>手順 4:Internet Explorer の拡張機能を自動的に有効にする

Internet Explorer のすべての拡張機能は、インストーラーを実行するだけでなく、明示的に有効にしてからでないと使用できません。 グループ ポリシーを使用してアクセス パネル拡張機能を有効にするのには、次の手順に従います。

1. **[グループ ポリシー管理エディター]** ウィンドウで、選択した構成の種類に応じて、次のどちらかのパスに移動します。これは、「[手順 3:インストール パッケージを割り当てる](#step-3-assign-the-installation-package)」で選択した構成の種類によって変わります。

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
1. **[アドオンの一覧]** を右クリックし、 **[編集]** を選択します。

    ![[アドオンの一覧] を右クリックし、[編集] を選択します。](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. **[アドオンの一覧]** ウィンドウで、 **[有効]** を選択します。 次に、 **[オプション]** セクションで **[表示...]** をクリックします。

    ![[有効] をクリックし、[表示...] をクリックする](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. **[表示するコンテンツ]** ウィンドウで、次の手順を実行します。

   1. 1 列目の **[値の名前]** フィールドには、次のクラス ID をコピーして貼り付けます。`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. 2 列目の **[値]** フィールドには、次の値を入力します。`1`
   3. **[OK]** をクリックして **[表示するコンテンツ]** ウィンドウを閉じます。

      ![前の手順で指定した値を入力する](./media/deploy-access-panel-browser-extension/show-contents.png)

1. **[OK]** をクリックして変更を適用し、 **[アドオンの一覧]** ウィンドウを閉じます。

選択した OU のコンピューターで拡張機能が有効になります。 [グループ ポリシーを使用して Internet Explorer のアドオンを有効または無効にする方法の詳細については、こちらを参照してください。](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>手順 5 (省略可能):パスワードを保存するかどうかを確認するプロンプトを無効にする

ユーザーがアクセス パネル拡張機能を使用してサインインするときに、Internet Explorer でパスワードを保存するかどうかを確認する次のプロンプトが表示されることがあります。

![パスワードを保存するかどうかを確認するプロンプトの表示](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

ユーザーに対してこのメッセージが表示されないようにする場合は、次の手順に従って、パスワードの保存によるオートコンプリートを禁止します。

1. **グループ ポリシー管理エディター** ウィンドウで、次のパスに移動します。 この構成設定は **[ユーザー構成]** でのみ利用できます。

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. **[フォームのユーザー名とパスワードのオートコンプリート機能を有効にする]** という名前の設定を見つけます。

   > [!NOTE]
   > 以前のバージョンの Active Directory では、この設定は、 **[パスワードを保存するオートコンプリートを許可しない]** という名前で表示される場合があります。 その設定の構成は、このチュートリアルで説明する設定とは異なります。

    ![これは [ユーザー設定] から探すことに注意](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. 上記の設定を右クリックし、 **[編集]** を選択します。
1. **[フォームのユーザー名とパスワードのオートコンプリート機能を有効にする]** というタイトルのウィンドウで、 **[無効]** を選択します。

    ![オートコンプリート機能を有効にする設定に対して [無効] を選択](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. **[OK]** をクリックしてこれらの変更を適用し、ウィンドウを閉じます。

これで、ユーザーは資格情報を保存することも、オートコンプリートを使用して前に保存された資格情報にアクセスすることもできなくなります。 ただし、このポリシーでは、他の種類のフォーム フィールド (検索フィールドなど) でのオートコンプリートは引き続き許可されます。

> [!WARNING]
> ユーザーがいくつかの資格情報を保存した後でこのポリシーが有効になった場合、既に保存されている資格情報がこのポリシーによって消去されることは*ありません*。

## <a name="step-6-testing-the-deployment"></a>手順 6:デプロイのテスト

拡張機能のデプロイメントが成功したかどうかを確認するには、次の手順に従います。

1. **[コンピューターの構成]** を使用してデプロイした場合は、「[手順 2:グループ ポリシー オブジェクトを作成する](#step-2-create-the-group-policy-object)」で選択した OU に属しているクライアント コンピューターにサインインします。 **[ユーザーの構成]** を使用してデプロイした場合は、その OU に属しているユーザーとしてサインインします。
1. このコンピューターでグループ ポリシーの変更を完全に更新するために、2 回サインインする場合があります。 強制的に更新するには、 **[コマンド プロンプト]** ウィンドウを開き、次のコマンドを実行します。`gpupdate /force`
1. インストールが行われるには、コンピューターを再起動する必要があります。 起動には、通常の拡張機能のインストールよりかなり長い時間がかかる可能性があります。
1. コンピューターが再起動したら、 **Internet Explorer**を開きます。 ウィンドウの右上隅の **[ツール]** (歯車アイコン) をクリックし、 **[アドオンの管理]** をクリックします。
1. **[アドオンの管理]** ウィンドウで、 **[Access Panel Extension]** がインストールされていて、 **[状態]** が **[有効]** に設定されていることを確認します。

   ![Access Panel Extension がインストールされ、有効になっていることを確認する](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>詳細情報

* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](what-is-single-sign-on.md)
* [Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](manage-access-panel-browser-extension.md)
