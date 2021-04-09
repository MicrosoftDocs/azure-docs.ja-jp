---
title: Windows Virtual Desktop MSIX アプリ アタッチ ポータル (プレビュー) - Azure
description: Azure portal を使用して MSIX アプリ アタッチを Windows Virtual Desktop 用に設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c775d81b88c891d6d8ea0a4597b4fa4fee29c86a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737547"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Azure portal で MSIX アプリのアタッチを設定する

> [!IMPORTANT]
> 現在、MSIX アプリのアタッチはパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Windows Virtual Desktop 環境で MSIX アプリ アタッチ (プレビュー) を設定する方法について説明します。

## <a name="requirements"></a>必要条件

>[!IMPORTANT]
>作業を開始する前に、必ず[こちらのフォーム](https://aka.ms/enablemsixappattach)に記入して送信し、サブスクリプションで MSIX アプリ アタッチを有効にしてください。 承認されたリクエストがない場合、MSIX アプリ アタッチは機能しません。 リクエストの承認には、営業日に最大で 24 時間かかる可能性があります。 リクエストが受諾されて完了すると、電子メールが届きます。

MSIX アプリ アタッチを構成するために必要な項目を次に示します。

- 機能する Windows Virtual Desktop のデプロイ。 Windows Virtual Desktop (classic) のデプロイ方法については、「[Windows Virtual Desktop でテナントを作成する](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)」を参照してください。 Azure Resource Manager の統合を使用して Windows Virtual Desktop をデプロイする方法については、「[Azure portal を使用してホスト プールを作成する](./create-host-pools-azure-marketplace.md)」を参照してください。
- 少なくとも 1 つのアクティブなセッション ホストが含まれている Windows Virtual Desktop ホスト プール。
- このホスト プールは検証環境に存在している必要があります。 
- MSIX パッケージ化ツール。
- MSIX のパッケージされたアプリケーションは、ファイル共有にアップロードされる MSIX イメージに展開されます。
- MSIX パッケージが格納される Windows Virtual Desktop デプロイ内のファイル共有。
- MSIX イメージをアップロードしたファイル共有には、ホスト プール内のすべての仮想マシン (VM) からアクセスできる必要もあります。 ユーザーには、イメージにアクセスするための読み取り専用アクセス許可が必要になります。
- 証明書が公的に信頼されていない場合は、「[証明書をインストールする](app-attach.md#install-certificates)」の手順に従ってください。

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>MSIX アプリ アタッチ アプリケーションの自動更新をオフにする

作業を開始する前に、MSIX アプリ アタッチ アプリケーションの自動更新を無効にする必要があります。 自動更新を無効にするには、管理者特権でのコマンド プロンプトで次のコマンドを実行する必要があります。

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Hyper-v を有効にした後で、仮想マシンを再起動することをお勧めします。

## <a name="configure-the-msix-app-attach-management-interface"></a>MSIX アプリ アタッチ管理インターフェイスを構成する

次に、Azure portal 用の MSIX アプリ アタッチ管理インターフェイスをダウンロードして構成する必要があります。

管理インターフェイスを設定するには、次のようにします。

1. [Azure portal を開きます](https://portal.azure.com)。
2. この拡張機能を信頼できるかどうかを尋ねるプロンプトが表示されたら、 **[許可]** を選択します。

      > [!div class="mx-imgBorder"]
      > ![信頼されていない拡張機能ウィンドウのスクリーンショット。 [許可] が赤で強調表示されています。](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>MSIX イメージをホスト プールに追加する

次に、MSIX イメージをホスト プールに追加する必要があります。

MSIX イメージを追加するには、次のようにします。

1. Azure portal を開きます。

2. 検索バーに「**Windows Virtual Desktop**」と入力し、サービス名を選択します。

      > [!div class="mx-imgBorder"]
      > ![Azure portal で、ユーザーが検索バーのドロップダウン メニューから [Windows Virtual Desktop] を選択しているスクリーンショット。 [Windows Virtual Desktop] が赤で強調表示されています。](media/find-and-select.png)

3. MSIX アプリを配置するホスト プールを選択します。

4. **[MSIX packages]\(MSIX パッケージ\)** を選択して、現在ホスト プールに追加されているすべての **MSIX パッケージ** が含まれているデータ グリッドを開きます。

5. **[+追加]** を選択して、 **[Add MSIX package]\(MSIX パッケージの追加\)** タブを開きます。

6. **[Add MSIX package]\(MSIX パッケージの追加\)** タブで、次の値を入力します。

      - **[MSIX image path]\(MSIX イメージのパス\)** には、ファイル共有上の MSIX イメージを指す有効な UNC パスを入力します。 (例: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd`)。完了したら、 **[追加]** を選択し、MSIX コンテナーを調べてそのパスが有効かどうかを確認します。

      - **[MSIX package]\(MSIX パッケージ\)** では、ドロップダウン メニューから関連する MSIX パッケージ名を選択します。 このメニューは、 **[MSIX image path]\(MSIX イメージのパス\)** に有効なイメージ パスを入力した場合にのみ設定されます。

      - **[Package applications]\(アプリケーションのパッケージ\)** では、MSIX パッケージ内でユーザーが使用できるようにするすべての MSIX アプリケーションが一覧に含まれていることを確認してください。

      - 必要に応じて、ユーザー デプロイで、パッケージをよりユーザーフレンドリにしたい場合は、 **[表示名]** を入力します。

      - **[バージョン]** に正しいバージョン番号が指定されていることを確認します。

      - 使用する **[Registration type]\(登録の種類\)** を選択します。 どれを使用するかは、ニーズによって異なります。

    - **[On-demand registration]\(オンデマンド登録\)** では、ユーザーがアプリケーションを起動するまで、MSIX アプリケーションの完全な登録が延期されます。 こちらの登録の種類を使用することをお勧めします。

    - **[Log on blocking]\(ログオン ブロック\)** では、ユーザーがサインインしている間だけ登録されます。 この種類ではユーザーのサインイン時間が長くなる可能性があるため、お勧めしません。

7.  **[状態]** では、希望する状態を選択します。
    -  **[アクティブ]** 状態の場合、ユーザーはパッケージを操作できます。
    -  **[非アクティブ]** 状態の場合は、Windows Virtual Desktop によってパッケージは無視されて、ユーザーに配布されません。

8. 終了したら、 **[追加]** を選択します。

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX アプリをアプリ グループに発行する

次に、アプリをパッケージに発行する必要があります。 これは、デスクトップとリモート アプリの両方のアプリケーション グループに対して行う必要があります。

MSIX イメージが既にある場合は、スキップして、「[MSIX アプリをアプリ グループに発行する](#publish-msix-apps-to-an-app-group)」に進んでください。 レガシ アプリケーションをテストする場合は、「[VM 上でデスクトップ インストーラーからの MSIX パッケージを作成する](/windows/msix/packaging-tool/create-app-package-msi-vm/)」の手順に従って、レガシ アプリケーションを MSIX パッケージに変換します。

アプリを発行するには、次のようにします。

1. Windows Virtual Desktop リソース プロバイダーで、 **[アプリケーション グループ]** タブを選択します。

2. アプリを発行するアプリケーション グループを選択します。

   >[!NOTE]
   >MSIX アプリケーションは、MSIX アプリ アタッチを使用して、リモート アプリとデスクトップ アプリの両方のグループに配布できます。

3. アプリ グループに入ったら、 **[アプリケーション]** タブを選択します。 **[アプリケーション]** グリッドには、アプリ グループ内のすべての既存のアプリが表示されます。

4. **[+追加]** を選択して **[アプリケーションの追加]** タブを開きます。

      > [!div class="mx-imgBorder"]
      > ![ユーザーが [+追加] を選択して [アプリケーションの追加] タブを開いているスクリーンショット](media/select-add.png)

5. **[アプリケーション ソース]** では、アプリケーションのソースを選択します。
    - デスクトップ アプリ グループを使用している場合は、 **[MSIX パッケージ]** を選択します。
      
      > [!div class="mx-imgBorder"]
      > ![ユーザーが [アプリケーション ソース] のドロップダウン メニューから [MSIX パッケージ] を選択しているスクリーンショット。 [MSIX パッケージ] が赤で強調表示されています。](media/select-source.png)
    
    - リモート アプリ グループを使用している場合は、次のいずれかのオプションを選択します。
        
        - [スタート] メニュー
        - アプリのパス
        - MSIX パッケージ

    - **[アプリケーション名]** には、アプリケーションのわかりやすい名前を入力します。

    次のオプション機能を構成することもできます。
   
    - **[表示名]** には、ユーザーに表示されるパッケージの新しい名前を入力します。

    - **[説明]** には、アプリ パッケージの簡単な説明を入力します。

    - リモート アプリ グループを使用している場合は、これらのオプションも構成できます。

        - **Icon path (アイコン パス)**
        - **Icon Index (アイコン インデックス)**
        - **Show in web feed (Web フィードに表示する)**

6. 終了したら、 **[保存]** を選択します。

>[!NOTE]
>ユーザーが同じホスト プールのリモート アプリ グループとデスクトップ アプリ グループに割り当てられている場合、フィードにはデスクトップ アプリグループが表示されます。

## <a name="assign-a-user-to-an-app-group"></a>ユーザーをアプリ グループに割り当てる

MSIX アプリをアプリ グループに割り当てたら、ユーザーにそれに対するアクセス権を付与する必要があります。 アクセス権を割り当てるには、発行されている MSIX アプリケーションが含まれたアプリ グループにユーザーまたはユーザー グループを追加します。 「[Azure portal を使用してアプリ グループを管理する](manage-app-groups.md)」の手順に従って、ユーザーをアプリ グループに割り当てます。

>[!NOTE]
>パブリック プレビュー中にリモート アプリをテストするときに、MSIX アプリ アタッチのリモート アプリがフィードから消えることがあります。 このアプリが表示されないのは、評価環境で使用しているホスト プールが運用環境の RD ブローカーによって提供されているためです。 運用環境の RD ブローカーによって MSIX アプリ アタッチのリモート アプリの存在が登録されないため、このアプリはフィードに表示されません。

## <a name="change-msix-package-state"></a>MSIX パッケージの状態を変更する

次に、パッケージの処理内容に応じて、MSIX パッケージの状態を **[アクティブ]** または **[非アクティブ]** に変更する必要があります。 アクティブ パッケージとは、発行後にユーザーが操作できるパッケージのことです。 非アクティブ パッケージは Windows Virtual Desktop によって無視されるため、ユーザーは内部のアプリを操作できません。

### <a name="change-state-with-the-applications-list"></a>アプリケーション一覧を使用して状態を変更する

アプリケーション一覧を使用してパッケージの状態を変更するするには、次のようにします。

1. ホスト プールにアクセスし、 **[MSIX パッケージ]** を選択します。 ホスト プール内の既存のすべての MSIX パッケージの一覧が表示されます。

2. 状態を変更する必要がある MSIX パッケージを選択し、 **[状態の変更]** を選択します。

### <a name="change-state-with-update-package"></a>更新パッケージを使用して状態を変更する

更新パッケージを使用してパッケージの状態を変更するには、次のようにします。

1. ホスト プールにアクセスし、 **[MSIX パッケージ]** を選択します。 ホスト プール内の既存のすべての MSIX パッケージの一覧が表示されます。

2. MSIX パッケージの一覧から、状態を変更するパッケージの名前を選択します。 これにより、 **[更新パッケージ]** タブが開きます。

3. **[状態]** スイッチを **[非アクティブ]** または **[アクティブ]** に切り替えて、 **[保存]** を選択します。

## <a name="change-msix-package-registration-type"></a>MSIX パッケージの登録の種類を変更する

パッケージの登録の種類を変更するには、次のようにします。

1. **[MSIX パッケージ]** を選択します。 ホスト プール内の既存のすべての MSIX パッケージの一覧が表示されます。

2. **MSIX パッケージ グリッド** から **パッケージ名** を選択します。これにより、パッケージを更新するためのブレードが開きます。

3. 必要に応じて **[On-demand/Log on blocking]\(オンデマンド/ログオン ブロック\)** ボタンで **[登録の種類]** を切り替えて、 **[保存]** を選択します。

## <a name="remove-an-msix-package"></a>MSIX パッケージを削除する

MSIX パッケージをホスト プールから削除するには、次のようにします。

1. **[MSIX パッケージ]** を選択します。  ホスト プール内の既存のすべての MSIX パッケージの一覧が表示されます。

2. 削除するパッケージ名の右側にある省略記号を選択し、 **[削除]** を選択します。

## <a name="remove-msix-apps"></a>MSIX アプリを削除する

パッケージから個々の MSIX アプリを削除するには、次のようにします。

1. ホスト プールにアクセスし、 **[アプリケーション グループ]** を選択します。

2. MSIX アプリを削除するアプリケーション グループを選択します。

3. **[アプリケーション]** タブを開きます。

4. 削除するアプリを選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

[Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスして、コミュニティにこの機能に関する質問をします。

また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。

役に立つその他の記事を次に示します。

- [MSIX アプリのアタッチの用語集](app-attach-glossary.md)
- [MSIX アプリ アタッチに関するごよくある質問](app-attach-faq.md)
