---
title: "StorSimple デバイスの Web プロキシを設定する | Microsoft Docs"
description: "StorSimple 用 Windows PowerShell を使用して StorSimple デバイス用に Web プロキシを構成する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c2ca351-a7c6-4da6-ab5e-c081e6d08261
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 66ee6ce15e51b14366eac0512c899d1c425c6092


---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>StorSimple デバイスの Web プロキシを構成する
## <a name="overview"></a>Overview
このチュートリアルでは、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの Web プロキシ設定を構成および表示する方法について説明します。 Web プロキシ設定は、クラウドと通信するときに StorSimple デバイスで使用されます。 Web プロキシ サーバーは、別のセキュリティ レイヤーの追加、コンテンツのフィルター処理、キャッシュによる帯域幅要件の緩和、分析の補助に使用されます。

Web プロキシは StorSimple デバイスのオプションの構成です。 Web プロキシの構成には StorSimple 用 Windows PowerShell のみ使用できます。 構成には、次の&2; つの手順を実行します。

1. まず、セットアップ ウィザードまたは StorSimple 用 Windows PowerShell のコマンドレットを使用して、Web プロキシ設定を構成します。
2. 次に、StorSimple 用 Windows PowerShell のコマンドレットを使用して、構成した Web プロキシ設定を有効にします。

Web プロキシの構成が完了したら、Microsoft Azure StorSimple Manager サービスと StorSimple 用 Windows PowerShell の両方で、構成済みの Web プロキシ設定を表示できます。 

このチュートリアルを読むと、次のことができるようになります。

* セットアップ ウィザードとコマンドレットを使用して Web プロキシを構成する
* コマンドレットを使用して Web プロキシを有効にする
* Azure クラシック ポータルで Web プロキシ設定を表示する
* Web プロキシの構成中のエラーをトラブルシューティングする

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用して Web プロキシを構成する
Web プロキシ設定を構成するには、次のいずれかを使用します。

* 構成手順を案内するセットアップ ウィザード。
* StorSimple 用 Windows PowerShell のコマンドレット。

これらの各方法について、以下のセクションで説明します。

## <a name="configure-web-proxy-via-the-setup-wizard"></a>セットアップ ウィザードを使用して Web プロキシを構成する
セットアップ ウィザードを使用し、案内に従って Web プロキシの構成手順を実行できます。 次の手順を実行して、デバイスで Web プロキシを構成します。

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>セットアップ ウィザードを使用して Web プロキシを構成するには
1. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]** を選択し、**デバイス管理者のパスワード**を入力します。 次のコマンドを入力してセットアップ ウィザード セッションを開始します。
   
    `Invoke-HcsSetupWizard`
2. デバイスの登録に初めてセットアップ ウィザードを使用する場合は、Web プロキシ構成の前に表示される、必要なすべてのネットワーク設定を構成する必要があります。 デバイスが既に登録されている場合は、Web プロキシ構成に達するまで、構成済みのすべてのネットワーク設定をそのまま使用することができます。 セットアップ ウィザードで、Web プロキシ設定を構成するかどうかを確認するメッセージが表示されたら、「 **Yes**」と入力します。
3. **[Web Proxy URL]**には、Web プロキシ サーバーの IP アドレスまたは完全修飾ドメイン名 (FQDN) と、クラウドと通信するときにデバイスで使用する TCP ポート番号を指定します。 次の形式を使用します。
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    既定では、TCP ポート番号 8080 が指定されています。
4. 認証の種類として、**[NTLM]**、**[Basic]**、または **[None]** を選択します。 [Basic] は、プロキシ サーバー構成で最も安全性が低い認証です。 [NTLM] \(NT LAN Manager) は、ユーザーを認証するために&3; 段階のメッセージング システム (さらに整合性が要求される場合は&4; 段階の場合もあります) を使用する最も安全性が高く複雑な認証プロトコルです。 既定の認証は NTLM です。 詳細については、[基本認証](http://hc.apache.org/httpclient-3.x/authentication.html)に関するページと[NTLM 認証](http://hc.apache.org/httpclient-3.x/authentication.html)に関するページを参照してください。 
   
   > [!IMPORTANT]
   > **StorSimple Manager サービスのデバイス監視グラフは、デバイスのプロキシ サーバーの構成で基本認証または NTLM 認証が有効になっている場合に正しく機能しません。監視グラフを機能させるには、認証が [None] に設定されていることを確認する必要があります。**
   > 
   > 
5. 認証を使用している場合は、**Web プロキシ ユーザー名**と **Web プロキシ パスワード**を指定します。 また、パスワードの確認も必要です。
   
    ![Configure Web Proxy On StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

初めてデバイスを登録する場合は、登録に進みます。 デバイスが既に登録されている場合、ウィザードは終了します。 構成した設定は保存されます。

また、Web プロキシも有効になります。 「[Web プロキシを有効にする](#enable-web-proxy)」の手順を省略し、すぐに、「[Azure クラシック ポータルで Web プロキシ設定を表示する](#view-web-proxy-settings-in-the-azure-classic-portal)」に進むことができます。

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>StorSimple 用 Windows PowerShell のコマンドレットを使用して Web プロキシを構成する
Web プロキシ設定を構成するための代替手段として、StorSimple 用 Windows PowerShell のコマンドレットを使用する方法があります。 次の手順を実行して、Web プロキシを構成します。

#### <a name="to-configure-web-proxy-via-cmdlets"></a>コマンドレットを使用して Web プロキシを構成するには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。 画面の指示に従って、 **デバイス管理者のパスワード**を入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトに、次のコマンドを入力します。
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    入力を求めるメッセージが次のように表示されたら、パスワードを入力して確認します。
   
    ![Configure Web Proxy On StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

これで、Web プロキシが構成されたので、有効にする必要があります。

## <a name="enable-web-proxy"></a>Web プロキシを有効にする
Web プロキシは、既定では無効になっています。 StorSimple デバイスで Web プロキシ設定を構成したら、StorSimple 用 Windows PowerShell を使用して Web プロキシ設定を有効にする必要があります。

> [!NOTE]
> **セットアップ ウィザードを使用して Web プロキシを構成した場合、この手順は必要ありません。セットアップ ウィザードのセッションの後、Web プロキシは既定で自動的に有効になります。**
> 
> 

StorSimple 用 Windows PowerShell で次の手順を実行し、デバイスで Web プロキシを有効にします。

#### <a name="to-enable-web-proxy"></a>Web プロキシを有効にするには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。 画面の指示に従って、 **デバイス管理者のパスワード**を入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトに、次のコマンドを入力します。
   
    `Enable-HcsWebProxy`
   
    これで、StorSimple デバイスの Web プロキシ構成が有効になりました。
   
    ![Configure Web Proxy On StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Azure クラシック ポータルで Web プロキシ設定を表示する
Web プロキシ設定は、Windows PowerShell インターフェイスを使用して構成します。クラシック ポータル内から変更することはできません。 ただし、構成済みの設定をクラシック ポータルで表示することはできます。 次の手順を実行して、Web プロキシを表示します。

#### <a name="to-view-web-proxy-settings"></a>Web プロキシ設定を表示するには
1. **[StorSimple Manager サービス]、[デバイス]** の順に移動します。 デバイスを選択してクリックし、 **[構成]**に移動します。
2. **[構成]** ページで **[Web プロキシの設定]** セクションまで下へスクロールします。 次に示すように、StorSimple デバイスで構成した Web プロキシ設定を表示できます。
   
    ![管理ポータルで Web プロキシを表示する](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## <a name="errors-during-web-proxy-configuration"></a>Web プロキシ構成中のエラー
Web プロキシの設定が正しく構成されていない場合は、StorSimple 用 Windows PowerShell でユーザーにエラー メッセージが表示されます。 次の表では、このようなエラー メッセージ、考えられる原因、および推奨される操作について説明します。

| シリアル番号 | HRESULT エラー コード | 考えられる根本原因 | 推奨される操作 |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |コマンドがパッシブ コントローラーから実行されていて、アクティブ コントローラーと通信することができません。 |アクティブ コントローラーでコマンドを実行します。 パッシブ コントローラーからコマンドを実行するには、接続をパッシブ コントローラーからアクティブ コントローラーに修正する必要があります。 この接続を確立できない場合、Microsoft サポートに問い合わせる必要があります。 |
| 手順&2;. |0x800710dd - 操作識別子が無効です |StorSimple 仮想デバイスではプロキシ設定がサポートされていません。 |StorSimple 仮想デバイスではプロキシ設定がサポートされていません。 これらの設定は、StorSimple の物理デバイスでのみ構成できます。 |
| 手順&3;. |0x80070057 - 無効なパラメーター |プロキシ設定に指定されたパラメーターの&1; つが有効ではありません。 |URI が正しい形式で指定されていません。 形式は `http://<IP address or FQDN of the web proxy server>:<TCP port number>` を使用してください。 |
| 4. |0x800706ba - RPC サーバーを利用できません |根本原因は、次のいずれかです。</br></br>クラスターが稼働していません。</br></br>データパス サービスが実行されていません。</br></br>コマンドがパッシブ コントローラーから実行されていて、アクティブ コントローラーと通信することができません。 |Microsoft サポートに問い合わせて、クラスターが稼働していて、データパス サービスが実行されていることを確認します。</br></br>アクティブ コントローラーからコマンドを実行します。 パッシブ コントローラーからコマンドを実行する場合、パッシブ コントローラーがアクティブ コントローラーと通信できることが必要となります。 この接続を確立できない場合、Microsoft サポートに問い合わせる必要があります。 |
| 5. |0x800706be - RPC 呼び出しに失敗しました |クラスターはダウンしています。 |Microsoft サポートに問い合わせて、クラスターが稼働しているかどうかを確認してください。 |
| 6. |0x8007138f - クラスター リソースが見つかりません |プラットフォーム サービス クラスター リソースが見つかりません。 これはインストールが適切でなかった場合に発生することがあります。 |デバイスを工場出荷時の既定値にリセットすることが必要になる場合があります。 プラットフォーム リソースの作成が必要になる場合があります。 Microsoft サポートに対処法をお問い合わせください。 |
| 7. |0x8007138c - クラスター リソースがオンラインではありません |プラットフォームまたはデータパス クラスター リソースがオンラインではありません。 |Microsoft サポートに連絡し、データパスとプラットフォーム サービス リソースがオンラインになるようにしてください。 |

> [!NOTE]
> * 上記のエラー メッセージの一覧ですべてを網羅しているわけではありません。 
> * Web プロキシの設定に関連するエラーは、StorSimple Manager サービスの Azure クラシック ポータルには表示されません。 構成が完了した後で Web プロキシに問題がある場合、クラシック ポータルではデバイスの状態が **"オフライン"** になります。
> 
> 

## <a name="next-steps"></a>次のステップ
* デバイスのデプロイまたは Web プロキシ設定の構成中に問題が発生した場合は、 [StorSimple デバイスのデプロイのトラブルシューティング](storsimple-troubleshoot-deployment.md)に関するページを参照してください。
* StorSimple Manager サービスを使用する方法については、「 [StorSimple Manager サービスを使用した StorSimple デバイスの管理](storsimple-manager-service-administration.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


