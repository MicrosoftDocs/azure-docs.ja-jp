---
title: StorSimple 8000 シリーズ デバイス用の Web プロキシを設定する | Microsoft Docs
description: StorSimple 用 Windows PowerShell を使用して StorSimple デバイス用に Web プロキシを構成する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204259"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>StorSimple デバイスの Web プロキシを構成する

## <a name="overview"></a>概要

このチュートリアルでは、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの Web プロキシ設定を構成および表示する方法について説明します。 Web プロキシ設定は、クラウドと通信するときに StorSimple デバイスで使用されます。 Web プロキシ サーバーは、別のセキュリティ レイヤーの追加、コンテンツのフィルター処理、キャッシュによる帯域幅要件の緩和、分析の補助に使用されます。

このチュートリアルのガイダンスは、StorSimple 8000 シリーズの物理デバイスにのみ適用されます。 Web プロキシ構成は、StorSimple Cloud Appliance (8010 および 8020) ではサポートされません。

Web プロキシは StorSimple デバイス用の _オプション_ の構成です。 Web プロキシの構成には StorSimple 用 Windows PowerShell のみ使用できます。 構成には、次の 2 つの手順を実行します。

1. まず、セットアップ ウィザードまたは StorSimple 用 Windows PowerShell のコマンドレットを使用して、Web プロキシ設定を構成します。
2. 次に、StorSimple 用 Windows PowerShell のコマンドレットを使用して、構成した Web プロキシ設定を有効にします。

Web プロキシの構成が完了したら、Microsoft Azure StorSimple デバイス マネージャー サービスと StorSimple 用 Windows PowerShell の両方で、構成済みの Web プロキシ設定を表示できます。

このチュートリアルを読むと、次のことができるようになります。

* セットアップ ウィザードとコマンドレットを使用して Web プロキシを構成する。
* コマンドレットを使用して Web プロキシを有効にする。
* Azure ポータルで Web プロキシ設定を表示する。
* Web プロキシの構成中のエラーをトラブルシューティングする。


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用して Web プロキシを構成する

Web プロキシ設定を構成するには、次のいずれかを使用します。

* 構成手順を案内するセットアップ ウィザード。
* StorSimple 用 Windows PowerShell のコマンドレット。

これらの各方法について、以下のセクションで説明します。

## <a name="configure-web-proxy-via-the-setup-wizard"></a>セットアップ ウィザードを使用して Web プロキシを構成する

セットアップ ウィザードを使用し、案内に従って Web プロキシの構成手順を実行します。 次の手順を実行して、デバイスで Web プロキシを構成します。

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>セットアップ ウィザードを使用して Web プロキシを構成するには

1. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]** を選択し、**デバイス管理者のパスワード**を入力します。 次のコマンドを入力してセットアップ ウィザード セッションを開始します。
   
    `Invoke-HcsSetupWizard`
2. デバイスを登録するためにセットアップ ウィザードを初めて使用する場合は、Web プロキシを構成する前に、必要なすべてのネットワーク設定を構成する必要があります。 デバイスが既に登録されている場合は、構成済みのすべてのネットワーク設定をそのまま使用して、Web プロキシを構成できます。 セットアップ ウィザードで、Web プロキシ設定を構成するかどうかを確認するメッセージが表示されたら、「 **Yes**」と入力します。
3. **[Web Proxy URL]** には、Web プロキシ サーバーの IP アドレスまたは完全修飾ドメイン名 (FQDN) と、クラウドと通信するときにデバイスで使用する TCP ポート番号を指定します。 次の形式を使用します。
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    既定では、TCP ポート番号 8080 が指定されています。
4. 認証の種類として、 **[NTLM]** 、 **[Basic]** 、または **[None]** を選択します。 [Basic] は、プロキシ サーバー構成で最も安全性が低い認証です。 [NTLM] \(NT LAN Manager) は、ユーザーを認証するために 3 段階のメッセージング システム (さらに整合性が要求される場合は 4 段階の場合もあります) を使用する最も安全性が高く複雑な認証プロトコルです。 既定の認証は NTLM です。 詳細については、[基本認証](https://hc.apache.org/httpclient-3.x/authentication.html)に関するページと[NTLM 認証](https://hc.apache.org/httpclient-3.x/authentication.html)に関するページを参照してください。 
   
   > [!IMPORTANT]
   > **StorSimple デバイス マネージャー サービスのデバイス監視チャートは、デバイスのプロキシ サーバーの構成で基本認証または NTLM 認証が有効になっていると正しく機能しません。監視グラフを機能させるには、認証が [None] に設定されていることを確認する必要があります。**
  
5. 認証を有効にしている場合は、**Web プロキシ ユーザー名**と **Web プロキシ パスワード**を指定します。 また、パスワードの確認も必要です。
   
    ![Configure Web Proxy On StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

初めてデバイスを登録する場合は、登録に進みます。 デバイスが既に登録されている場合、ウィザードは終了します。 構成した設定は保存されます。

これで、Web プロキシが有効になりました。 「[Web プロキシを有効にする](#enable-web-proxy)」の手順を省略し、すぐに、「[Azure ポータルで Web プロキシ設定を表示する](#view-web-proxy-settings-in-the-azure-portal)」に進むことができます。

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>StorSimple 用 Windows PowerShell のコマンドレットを使用して Web プロキシを構成する

Web プロキシ設定を構成するための代替手段として、StorSimple 用 Windows PowerShell のコマンドレットを使用する方法があります。 次の手順を実行して、Web プロキシを構成します。

#### <a name="to-configure-web-proxy-via-cmdlets"></a>コマンドレットを使用して Web プロキシを構成するには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。 画面の指示に従って、 **デバイス管理者のパスワード**を入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトに、次のコマンドを入力します。
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    入力を求めるメッセージが表示されたら、パスワードを入力して確認します。
   
    ![Configure Web Proxy On StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

これで、Web プロキシが構成されたので、有効にする必要があります。

## <a name="enable-web-proxy"></a>Web プロキシを有効にする

Web プロキシは、既定では無効になっています。 StorSimple デバイスで Web プロキシ設定を構成したら、StorSimple 用 Windows PowerShell を使用して Web プロキシ設定を有効にします。

> [!NOTE]
> **セットアップ ウィザードを使用して Web プロキシを構成した場合、この手順は必要ありません。セットアップ ウィザードのセッションの後、Web プロキシは既定で自動的に有効になります。**


StorSimple 用 Windows PowerShell で次の手順を実行し、デバイスで Web プロキシを有効にします。

#### <a name="to-enable-web-proxy"></a>Web プロキシを有効にするには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。 画面の指示に従って、 **デバイス管理者のパスワード**を入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトに、次のコマンドを入力します。
   
    `Enable-HcsWebProxy`
   
    これで、StorSimple デバイスの Web プロキシ構成が有効になりました。
   
    ![Configure Web Proxy On StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Web プロキシ設定を Azure ポータルに表示する

Web プロキシ設定は、Windows PowerShell インターフェイスを使用して構成します。ポータル内から変更することはできません。 ただし、構成済みの設定をポータルに表示できます。 次の手順を実行して、Web プロキシを表示します。

#### <a name="to-view-web-proxy-settings"></a>Web プロキシ設定を表示するには
1. **[StorSimple デバイス マネージャー サービス]、[デバイス]** の順に移動します。 デバイスを選択してクリックし、 **[デバイス設定]、[ネットワーク]** の順に移動します。

    ![[ネットワーク] をクリックする](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. **[ネットワーク設定]** ブレードで、 **[Web プロキシ]** タイルをクリックします。

    ![Web プロキシをクリックする](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. **[Web プロキシ]** ブレードで、StorSimple デバイスに構成した Web プロキシ設定を確認します。
   
    ![Web プロキシ表示設定を構成する](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Web プロキシ構成中のエラー

Web プロキシの設定が正しく構成されていない場合は、StorSimple 用 Windows PowerShell でエラー メッセージが表示されます。 次の表では、このようなエラー メッセージ、考えられる原因、および推奨される操作について説明します。

| シリアル番号 | HRESULT エラー コード | 考えられる根本原因 | 推奨される操作 |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |コマンドがパッシブ コントローラーから実行されていて、アクティブ コントローラーと通信することができません。 |アクティブ コントローラーでコマンドを実行します。 パッシブ コントローラーからコマンドを実行するには、接続をパッシブ コントローラーからアクティブ コントローラーに修正する必要があります。 この接続を確立できない場合、Microsoft サポートに問い合わせる必要があります。 |
| 2. |0x800710dd - 操作識別子が無効です |StorSimple Cloud Appliance ではプロキシ設定はサポートされていません。 |StorSimple Cloud Appliance ではプロキシ設定はサポートされていません。 これらの設定は、StorSimple の物理デバイスでのみ構成できます。 |
| 3. |0x80070057 - 無効なパラメーター |プロキシ設定に指定されたパラメーターの 1 つが有効ではありません。 |URI が正しい形式で指定されていません。 形式は `http://<IP address or FQDN of the web proxy server>:<TCP port number>` を使用してください。 |
| 4. |0x800706ba - RPC サーバーを利用できません |根本原因は、次のいずれかです。</br></br>クラスターが稼働していません。 </br></br>データパス サービスが実行されていません。</br></br>コマンドがパッシブ コントローラーから実行されていて、アクティブ コントローラーと通信することができません。 |Microsoft サポートに問い合わせて、クラスターが稼働していて、データパス サービスが実行されていることを確認します。</br></br>コマンドをアクティブ コントローラーから実行します。 パッシブ コントローラーからコマンドを実行する場合、パッシブ コントローラーがアクティブ コントローラーと通信できることが必要となります。 この接続を確立できない場合、Microsoft サポートに問い合わせる必要があります。 |
| 5. |0x800706be - RPC 呼び出しに失敗しました |クラスターはダウンしています。 |Microsoft サポートに問い合わせて、クラスターが稼働していることを確認してください。 |
| 6. |0x8007138f - クラスター リソースが見つかりません |プラットフォーム サービス クラスター リソースが見つかりません。 これはインストールが適切でなかった場合に発生することがあります。 |デバイスを工場出荷時の既定値にリセットすることが必要になる場合があります。 プラットフォーム リソースの作成が必要になる場合があります。 Microsoft サポートに手順をお問い合わせください。 |
| 7. |0x8007138c - クラスター リソースがオンラインではありません |プラットフォームまたはデータパス クラスター リソースがオンラインではありません。 |Microsoft サポートに連絡し、データパスとプラットフォーム サービス リソースがオンラインになるようにしてください。 |

> [!NOTE]
> * 上記のエラー メッセージの一覧ですべてを網羅しているわけではありません。
> * Web プロキシの設定に関連するエラーは、StorSimple デバイス マネージャー サービスの Azure ポータルには表示されません。 構成が完了した後で Web プロキシに問題がある場合、クラシック ポータルではデバイスの状態が **"オフライン"** になります。

## <a name="next-steps"></a>次の手順
* デバイスのデプロイまたは Web プロキシ設定の構成中に問題が発生した場合は、 [StorSimple デバイスのデプロイのトラブルシューティング](storsimple-troubleshoot-deployment.md)に関するページを参照してください。
* StorSimple デバイス マネージャー サービスを使用する方法については、「[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)」を参照してください。

