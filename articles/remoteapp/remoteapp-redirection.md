---
title: "Azure RemoteApp でリダイレクトを使用する | Microsoft Docs"
description: "RemoteApp でリダイレクトを構成し、使用する方法を学習します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 2c8c867f-4907-4f2e-9ccd-2eb82bb5b837
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5a5ad526513c7adf9b8274acec7d89fe375f43fc


---
# <a name="using-redirection-in-azure-remoteapp"></a>Azure RemoteApp でリダイレクトを使用する
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

デバイス リダイレクトを使用すると、ユーザーはローカルのコンピューター、電話、タブレットに接続されているデバイスを利用し、リモート アプリと対話できます。 たとえば、Azure RemoteApp で Skype を指定した場合、ユーザーが Skype を操作するには PC にカメラを取り付ける必要があります。 これはプリンター、スピーカー、モニター、各種 USB 接続周辺機器についても当てはまります。

RemoteApp はリモート デスクトップ プロトコル (RDP) と RemoteFX を利用してリダイレクトを提供します。

## <a name="what-redirection-is-enabled-by-default"></a>リダイレクトは既定で有効になっていますか。
RemoteApp を使用するとき、次のリダイレクトが既定で有効になっています。 括弧内の情報は RDP 設定を示しています。

* ローカル コンピューターで音を再生します (**このコンピューターで再生します**)。 (audiomode:i:0)
* ローカル コンピューターから音声をキャプチャし、リモート コンピューターに送信します (**このコンピューターから記録します**)。 (audiocapturemode:i:1)
* ローカル プリンターに出力します (Redirectprinters:i:1)。
* COM ポート (redirectcomports:i:1)
* スマート カード デバイス (redirectsmartcards:i:1)
* クリップボード (コピーして貼り付ける機能) (redirectclipboard:i:1)
* ClearType フォント スムージング (allow font smoothing:i:1)
* サポートされているすべてのプラグ アンド プレイ デバイスをリダイレクトします。 (devicestoredirect:s:*)

## <a name="what-other-redirection-is-available"></a>他にはどのようなリダイレクトが利用できますか。
2 つのリダイレクト オプションが既定で無効になっています。

* ドライブ リダイレクト (ドライブ マッピング): ローカル コンピューターのドライブがリモート セッションでマッピングされたドライブになります。 これにより、リモート セッションで作業中に、ローカル ドライブからファイルを開いたり、保存したりできます。
* USB リダイレクト: リモート セッション内で、ローカル コンピューターに接続されている USB デバイスを使用できます。

## <a name="change-your-redirection-settings-in-remoteapp"></a>RemoteApp でリダイレクトの設定を変更します。
Microsoft Azure PowerShell と SDK を使用し、コレクションのデバイス リダイレクト設定を変更できます。 新しい PowerShell と SDK をインストールした後を構成したら、最初に「 [Azure PowerShell を構成する方法](/powershell/azureps-cmdlets-docs)」の説明に従い、サブスクリプションを管理するように設定します。

次のようなコマンドを利用し、カスタム RDP プロパティを設定します。

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(*`n* は個々のプロパティ間の区切り記号として使用されていることに注意してください。)

設定されているカスタム RDP プロパティの一覧を取得するには、次のコマンドレットに実行します。 カスタム プロパティだけが出力の結果として表示され、既定のプロパティは表示されないことに注意してください。  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

カスタム プロパティを設定するときは、毎回すべてのカスタム プロパティを指定する必要があります。指定しない場合、設定が無効に戻ります。   

### <a name="common-examples"></a>一般的な例
ドライブ リダイレクトを有効にするには、次のコマンドレットを使用します。  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*"

USB リダイレクトとドライブ リダイレクトの両方を有効にするには、このコマンドレットを使用します。

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

クリップボード共有を無効にするには、このコマンドレットを使用します。  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0"

> [!IMPORTANT]
> 変更をテストする前に、必ずコレクション内のすべてのユーザーを (切断するだけでなく) ログオフしてください。 ユーザーが完全にログオフさせるには、Azure ポータルのコレクションの **[セッション]** タブに移動し、切断されているユーザーまたはサインインしているユーザーをログオフします。 セッション内でローカル ドライブがエクスプローラーに表示されるまで数秒かかることがあります。
> 
> 

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Windows クライアントで USB リダイレクトの設定を変更する
RemoteApp に接続するコンピューターで USB リダイレクトを使用する場合、2 つの操作を行う必要があります。 1 - 管理者が Azure PowerShell を使用し、コレクション レベルで USB リダイレクトを有効にする必要があります。 2 - USB リダイレクトを使用する各デバイスで、それを許可するグループ ポリシーを有効にする必要があります。 この手順は、USB リダイレクトを使用するユーザーごとに実行する必要があります。

> [!NOTE]
> Azure RemoteApp による USB リダイレクトは Windows コンピューターでのみ可能です。
> 
> 

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>RemoteApp コレクションの USB リダイレクトを有効にする
コレクション レベルで USB リダイレクトを有効にするには、次のコマンドレットを使用します。

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>クライアント コンピューターの USB リダイレクトを有効にする
コンピューターで USB リダイレクト設定を構成するには

1. ローカル グループ ポリシー エディターを開きます (GPEDIT.MSC)。 (コマンド プロンプトから gpedit.msc を実行します。)
2. **Computer Configuration\Policies\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Connection Client\RemoteFX USB Device Redirection** を開きます。
3. **[このコンピューターからサポートされている他の RemoteFX USB デバイスの RDP リダイレクトを許可する]**をダブルクリックします。
4. **[有効]** を選択し、**[RemoteFX USB リダイレクト アクセス権の管理者とユーザー]** を選択します。
5. 管理者権限でコマンド プロンプトを開き、次のコマンドを実行します。
   
        gpupdate /force
6. コンピューターを再起動します。

グループ ポリシー管理ツールを利用し、USB リダイレクト ポリシーを作成してドメイン内のすべてのコンピューターに適用することもできます。

1. ドメイン管理者としてドメイン コントローラーにログインします。
2. グループ ポリシー管理コンソールを開きます。 (**[開始] > [管理ツール] > [グループ ポリシー管理]** の順にクリックします。)
3. ポリシーを作成するドメインまたは組織単位に移動します。
4. **[既定のドメイン ポリシー]** を右クリックし、**[編集]** をクリックします。
5. **Computer Configuration\Policies\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Connection Client\RemoteFX USB Device Redirection** を開きます。
6. **[このコンピューターからサポートされている他の RemoteFX USB デバイスの RDP リダイレクトを許可する]**をダブルクリックします。
7. **[有効]** を選択し、**[RemoteFX USB リダイレクト アクセス権の管理者とユーザー]** を選択します。
8. **[OK]**をクリックします。  




<!--HONumber=Dec16_HO2-->


