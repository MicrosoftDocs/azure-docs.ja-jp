---
title: StorSimple 8000 シリーズのデプロイのトラブルシューティング | Microsoft Docs
description: StorSimple を初めてデプロイするときに発生するエラーを診断し、修正する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566347"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>StorSimple デバイスのデプロイメントのトラブルシューティング
## <a name="overview"></a>概要
この記事は、Microsoft Azure StorSimple のデプロイに役立つトラブルシューティングのガイダンスです。 StorSimple の構成時に発生する可能性のある問題の解決に役立つ、一般的な問題、考えられる原因、推奨手順について説明します。 

この情報は、StorSimple 8000 シリーズ物理デバイスと StorSimple Cloud Appliance の両方に当てはまります。

> [!NOTE]
> デバイスの構成に関する問題は、デバイスを初めてデプロイするときやデバイスが省略可能な場合は、後ほど発生することがあります。 この記事では、初回デプロイメントでの問題のトラブルシューティングについて説明します。 運用デバイスのトラブルシューティングを行うには、[診断ツールを使用した運用デバイスのトラブルシューティング](storsimple-8000-diagnostics.md)に関するページを参照してください。

この記事ではまた、StorSimple のデプロイのトラブルシューティングのためのツールについて説明し、トラブルシューティングの手順の例を示します。

## <a name="first-time-deployment-issues"></a>初回デプロイメントの問題
デバイスの初回デプロイ時に問題が発生した場合は、次のガイダンスを確認してください。

* 物理デバイスのトラブルシューティングを行う場合、「[StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md)」または「[StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)」の説明に従ってハードウェアが設置され構成されていることをご確認ください。
* デプロイの前提条件を確認します。 [デプロイメント チェックリスト](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)に記載されているすべての情報が揃っていることを確認します。
* StorSimple のリリース ノートを読んで、該当する問題についての記述があるかどうかを確認します。 設置に関して既に確認されている問題については、リリース ノートに回避策が記述されています。 

デバイスをデプロイするときに最も多く見られる問題は、セットアップ ウィザードの実行時と StorSimple 用 Windows PowerShell によるデバイスの登録時に発生します (StorSimple デバイスの登録と構成は、StorSimple 用 Windows PowerShell を使用して行います。 デバイス登録の詳細については、「[手順 3:StorSimple 用 Windows PowerShell を使用してデバイスを構成して登録する](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください)。

次のセクションは、StorSimple デバイスを初めて構成するときに発生する問題を解決するのに役立ちます。

## <a name="first-time-setup-wizard-process"></a>初回セットアップ ウィザード プロセス
次の手順は、セットアップ ウィザードのプロセスの概要を示します。 セットアップの詳細については、「 [オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」をご覧ください。

1. [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) コマンドレットを実行してセットアップ ウィザードを起動し、画面の指示に従います。 
2. ネットワークの構成: セットアップ ウィザードの指示に従って、StorSimple デバイスに対する DATA 0 ネットワーク インターフェイスのネットワーク設定を構成します。 次の設定が含まれています。
   * 仮想 IP (VIP)、サブネット マスク、ゲートウェイ: [Set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) コマンドレットがバックグラウンドで実行されます。 StorSimple デバイスの DATA 0 ネットワーク インターフェイスの IP アドレス、サブネット マスク、ゲートウェイが構成されます。
   * プライマリ DNS サーバー: [Set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) コマンドレットがバックグラウンドで実行されます。 StorSimple ソリューションの DNS 設定が構成されます。
   * NTP サーバー – [Set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) コマンドレットがバックグラウンドで実行されます。 StorSimple ソリューションの NTP サーバーの設定が構成されます。
   * (省略可能) Web プロキシ: [Set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) コマンドレットがバックグラウンドで実行されます。 StorSimple ソリューションの Web プロキシの構成が設定され、有効化されます。
3. パスワードの設定: 次の手順では、デバイスの管理者パスワードを設定します。
   デバイス管理者のパスワードは、デバイスへのログオンに使用します。 デバイスの既定のパスワードは **Password1** です。
        
     > [!IMPORTANT]
     > パスワードは登録前に収集されますが、適用されるのは、デバイスの登録に成功した後になります。 パスワードの適用に失敗した場合、必要な (複雑さの要件を満たした) パスワードが収集されるまで、パスワードを再入力するように求められます。
     
4. デバイスの登録: 最後に、Microsoft Azure 内で実行されている StorSimple Device Manager サービスにデバイスを登録します。 登録するには、Azure ポータルから[サービス登録キー](storsimple-8000-manage-service.md#get-the-service-registration-key)を取得し、セットアップ ウィザードにキーを入力する必要があります。 **デバイスが正常に登録されると、サービス データ暗号化キーが支給されます。この暗号化キーは以後、他のすべてのデバイスをサービスに登録する際に必要となります。安全な場所に保管しておいてください。**

## <a name="common-errors-during-device-deployment"></a>デバイスのデプロイ時に発生する一般的なエラー
以下の表は、次の場合に発生する可能性のある一般的なエラーを示しています。

* 必要なネットワーク設定を構成する。
* オプションの Web プロキシ設定を構成する。
* デバイス管理者のパスワードを変更する。
* デバイスを登録する。

## <a name="errors-during-the-required-network-settings"></a>必要なネットワークの設定中のエラー
| いいえ。 | エラー メッセージ | 考えられる原因 | 推奨される操作 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard:このコマンドは、アクティブなコントローラーでのみ実行することができます。 |パッシブなコントローラーに対して構成を行った可能性があります。 |アクティブなコントローラーからコマンドを実行してください。 詳細については、「 [デバイスでのアクティブなコントローラーの識別](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)」を参照してください。 |
| 2 |Invoke-HcsSetupWizard:デバイスの準備ができていません。 |DATA 0 にネットワーク接続の問題があります。 |DATA 0 の物理的なネットワーク接続を確認してください。 |
| 3 |Invoke-HcsSetupWizard:ネットワーク上の別のシステムと競合する IP アドレスがあります (HRESULT からの例外:0x80070263)。 |DATA 0 に対して指定した IP は既に別のシステムによって使用されています。 |使用されていない新しい IP を指定してください。 |
| 4 |Invoke-HcsSetupWizard:クラスター リソースが失敗しました (HRESULT からの例外:0x800713AE)。 |VIP が重複しています。 指定された IP は既に使用されています。 |使用されていない新しい IP を指定してください。 |
| 5 |Invoke-HcsSetupWizard:IPv4 アドレスが無効です。 |指定された IP アドレスの形式に誤りがあります。 |形式を確認し、IP アドレスを指定し直してください。 詳細については、「[IPv4 のアドレス指定][1]」を参照してください。 |
| 6 |Invoke-HcsSetupWizard:IPv6 アドレスが無効です。 |指定された IP アドレスの形式に誤りがあります。 |形式を確認し、IP アドレスを指定し直してください。 詳細については、「[IPv6 のアドレス指定][2]」を参照してください。 |
| 7 |Invoke-HcsSetupWizard:エンドポイント マッパーから使用できるエンドポイントはこれ以上ありません (HRESULT からの例外:0x800706D9) |クラスターの機能が正常に動作していません。 |[Microsoft サポート](storsimple-8000-contact-microsoft-support.md) に対処法をお問い合わせください。 |

## <a name="errors-during-the-optional-web-proxy-settings"></a>省略可能な Web プロキシの設定中のエラー
| いいえ。 | エラー メッセージ | 考えられる原因 | 推奨される操作 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard:無効なパラメーター (HRESULT からの例外:0x80070057) |プロキシ設定に指定されたパラメーターの 1 つが有効ではありません。 |URI が正しい形式で指定されていません。 次の形式を使用します。 http:// *\<IP address or FQDN of the web proxy server>* : *\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard:RPC サーバーを利用できません (HRESULT からの例外:0x800706ba) |根本原因は、次のいずれかです。<ol><li>クラスターが稼働していません。</li><li>パッシブ コントローラーがアクティブ コントローラーと通信できません。コマンドがパッシブ コントローラーから実行されています。</li></ol> |根本原因に応じて対応します。<ol><li>[Microsoft サポートに問い合わせ](storsimple-8000-contact-microsoft-support.md) て、クラスターが稼働しているかどうかを確認します。</li><li>コマンドをアクティブ コントローラーから実行します。 パッシブ コントローラーからコマンドを実行する場合、パッシブ コントローラーがアクティブ コントローラーと通信できることが必要となります。 この接続を確立できない場合は、[Microsoft サポートに問い合わせる](storsimple-8000-contact-microsoft-support.md)必要があります。</li></ol> |
| 3 |Invoke-HcsSetupWizard:RPC コールに失敗しました (HRESULT からの例外:0x800706be) |クラスターはダウンしています。 |[Microsoft サポートに問い合わせ](storsimple-8000-contact-microsoft-support.md) て、クラスターが稼働しているかどうかを確認します。 |
| 4 |Invoke-HcsSetupWizard:クラスター リソースが見つかりません (HRESULT からの例外:0x8007138f) |クラスター リソースが見つかりません。 これはインストールが適切でなかった場合に発生することがあります。 |デバイスを工場出荷時の既定の設定にリセットすることが必要な場合があります。 [Microsoft サポートに問い合わせ](storsimple-8000-contact-microsoft-support.md) て、クラスター リソースを作成します。 |
| 5 |Invoke-HcsSetupWizard:クラスター リソースがオンラインになっていません (HRESULT からの例外:0x8007138c) |クラスター リソースがオンラインになっていません。 |[Microsoft サポート](storsimple-8000-contact-microsoft-support.md) に対処法をお問い合わせください。 |

## <a name="errors-related-to-device-administrator-password"></a>デバイス管理者のパスワードに関するエラー
デバイス管理者の既定のパスワードは **Password1** です。 このパスワードは、初回ログオン後に有効期限が切れるため、セットアップ ウィザードを使用して変更する必要があります。 デバイスの初回登録時に、デバイス管理者の新しいパスワードを指定してください。 

パスワードは次の要件を満たしている必要があります。

* デバイス管理者のパスワードは 8 ～ 15 文字とする必要があります。
* パスワードには、小文字、大文字、数字、および特殊文字のうちの 3 つの組み合わせを含める必要があります。 
* 直近 24 個のパスワードは使用できません。

パスワードは毎年更新する必要があります。パスワードは、デバイスの登録が成功した後でのみ変更されます。 何らかの理由で登録に失敗した場合、パスワードは変更されません。

デバイス管理者のパスワードの詳細については、「[StorSimple Device Manager サービスを使用した StorSimple のパスワードの変更](storsimple-8000-change-passwords.md)」をご覧ください。

デバイス管理者のパスワードまたは StorSimple Snapshot Manager のパスワードを設定するときに、以下に示したエラーが発生することがあります。エラーは複数発生する場合もあります。

| いいえ。 | エラー メッセージ | 推奨される操作 |
| --- | --- | --- |
| 1 |パスワードが最大長を超えています。 |デバイス管理者のパスワードは 8 ～ 15 文字とする必要があります。 |
| 2 |パスワードが、必要な長さを満たしていません。 |デバイス管理者のパスワードは 8 ～ 15 文字とする必要があります。|
| 3 |パスワードには、小文字を含める必要があります。 |パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 4 |パスワードには、数字を含める必要があります。 |パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 5 |パスワードには、特殊文字を含める必要があります。 |パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 6 |パスワードには、大文字、小文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 |ご使用のパスワードには、必要な文字種が含まれていません。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 7 |パスワードが確認内容と一致しません。 |パスワードがすべての要件を満たしていることと、正しく入力されていることを確認してください。 |
| 8 |既定のパスワードと同じパスワードは使用できません。 |既定のパスワードは *Password1* です。 このパスワードは、初回ログオン後に変更する必要があります。 |
| 9 |入力されたパスワードがデバイスのパスワードと一致しません。 パスワードを再入力してください。 |パスワードを確認し、もう一度入力します。 |

パスワードは、デバイスの登録前に収集されますが、適用されるのは、登録に成功した後になります。 パスワード復元ワークフローを使用するには、デバイスが登録されている必要があります。

> [!IMPORTANT]
> 通常、パスワードを適用できなかった場合、成功するまで繰り返しパスワードの入力を求められます。 まれに、パスワードを適用できないことがあります。 その場合、デバイスを登録して続行することもできますが、パスワードは変更されません。 Azure ポータルから登録した後で、デバイス管理者のパスワードを変更できます。


Azure ポータルで StorSimple Device Manager サービスを使用してパスワードをリセットできます。 詳細については、「[デバイス管理者パスワードを変更する](storsimple-8000-change-passwords.md#change-the-device-administrator-password)」を参照してください。

## <a name="errors-during-device-registration"></a>デバイス登録中のエラー
デバイスを登録するために、Microsoft Azure で実行されている StorSimple Device Manager サービスを使用します。 デバイスの登録中、次に示すエラーが発生することがあります。エラーは複数発生する場合もあります。

| いいえ。 | エラー メッセージ | 考えられる原因 | 推奨される操作 |
| --- | --- | --- | --- |
| 1 |エラー 350027:StorSimple Device Manager にデバイスを登録できませんでした。 | |しばらくしてから操作をやり直してください。 引き続き問題が発生する場合は、 [Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md)ください。 |
| 2 |エラー 350013:デバイスの登録中にエラーが発生しました。 サービス登録キーが正しくない可能性があります。 | |正しいサービス登録キーを使用して再度デバイスを登録してください。 詳細については、「 [サービス登録キーの取得](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |エラー 350063:StorSimple Device Manager サービスに対する認証は成功しましたが登録に失敗しました。 しばらくしてから、操作を再試行してください。 |このエラーは、ACS での認証には成功しましたが、サービスに対する登録呼び出しに失敗したことを示します。 突発的なネットワーク障害が原因として考えられます。 |問題が解決しない場合は、 [Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md)ください。 |
| 4 |エラー 350049:登録時にサービスに到達できませんでした。 |サービスの呼び出しで、Web 例外が発生しました。 後で操作を再試行すると解決する場合があります。 |IP アドレスと DNS 名を確認して、操作を再試行してください。 問題が解決しない場合は、 [Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md) |
| 5 |エラー 350031:デバイスは既に登録されています。 | |対処不要です。 |
| 6 |エラー 350016:デバイスの登録に失敗しました。 | |登録キーが正しいことを確認してください。 |
| 7 |Invoke-HcsSetupWizard:デバイスの登録中にエラーが発生しました。原因として、IP アドレスまたは DNS 名の誤りが考えられます。 原因として、IP アドレスまたは DNS 名の誤りが考えられます。 問題が解決しない場合は、 [contact Microsoft Support](storsimple-8000-contact-microsoft-support.md)に関するページを参照してください。 (エラー 350050)。 |デバイスから外部のネットワークに ping を実行できることを確認してください。 外部ネットワークに接続できない場合、このエラーが発生して登録に失敗することがあります。 このエラーは次の間違いが組み合わさって発生することがあります。<ul><li>不正確なIP</li><li>不正確なサブネット</li><li>不正確なゲートウェイ</li><li>不正確な DNS 設定</li></ul> |「 [ステップ バイ ステップのトラブルシューティングの例](#step-by-step-storsimple-troubleshooting-example)」で手順を参照してください。 |
| 8 |Invoke-HcsSetupWizard:サービスの内部エラー [0x1FBE2] が発生したため、現在の操作を実行できませんでした。 しばらくしてから、操作を再試行してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |これは、ユーザーからは見えないすべてのエラーについてサービスまたはエージェントからスローされる総称的なエラーです。 その最も一般的な原因として ACS 認証の失敗が挙げられます。 NTP サーバーの構成に問題があって、デバイス上の時刻が正しく設定されていない可能性があります。 |(問題がある場合は) 時刻を修正してから、登録操作を再試行してください。 Set-HcsSystem -Timezone コマンドを使用してタイム ゾーンを調整する場合、タイム ゾーンの各単語の先頭文字を大文字にします (例: "Pacific Standard Time")。  問題が解決しない場合は、 [Microsoft サポート](storsimple-8000-contact-microsoft-support.md) に対処法をお問い合わせください。 |
| 9 |警告:デバイスをアクティブ化できませんでした。 デバイス管理者のパスワードおよび StorSimple Snapshot Manager のパスワードが変更されていません。 |登録に失敗した場合、デバイス管理者のパスワードおよび StorSimple Snapshot Manager のパスワードは変更されません。 | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>StorSimple デプロイメントのトラブルシューティング用ツール
StorSimple には、StorSimple ソリューションのトラブルシューティングに使用できるツールがいくつか含まれています。 これには以下のツールが含まれます。

* サポート パッケージとデバイス ログ
* トラブルシューティング用に設計されたコマンドレット

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>トラブルシューティングに使用できるサポート パッケージとデバイス ログ
サポート パッケージには、Microsoft サポート チームがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログが含まれています。 StorSimple 用 Windows PowerShell を使用すると、暗号化されたサポート パッケージが生成され、サポート スタッフと共有できます。

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>サポート パッケージのログまたは内容を表示するには
1. 「 [サポート パッケージの作成](storsimple-8000-create-manage-support-package.md)」で説明したとおり、StorSimple 用 Windows PowerShell を使用してサポート パッケージを生成します。
2. クライアント コンピューターにローカルに [復号化スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) をダウンロードします。
3. この [手順](storsimple-8000-create-manage-support-package.md#edit-a-support-package) に従い、このサポート パッケージを開いて暗号化を解除します。
4. 暗号化が解除されたサポート パッケージのログは etw/etvx 形式です。 次の手順を実行すると、これらのファイルを Windows イベント ビューアーで表示できます。
   
   1. Windows クライアントで **eventvwr** コマンドを実行して、イベント ビューアーを起動します。
   2. **[操作]** ウィンドウで、 **[保存されたログを開く]** をクリックし、etvx/etw の形式のログ ファイル (サポート パッケージ) を参照します。 これで、ファイルを表示できます。 ファイルを開いた後、ファイルを右クリックし、テキストとして保存できます。
      
      > [!IMPORTANT]
      > また、Windows PowerShell の **Get-WinEvent** コマンドレットを使用して、これらのファイルを開くこともできます。 詳細については、Windows PowerShell コマンドレット リファレンス ドキュメントの「 [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) 」を参照してください。
     
5. イベント ビューアーでログを開いたら、デバイスの構成に関連する問題を含む次のログを探します。
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. ログ ファイル内で、セットアップ ウィザードによって呼び出されたコマンドレットに関連する文字列を検索します。 これらのコマンドレットの一覧については、「 [初回セットアップ ウィザード プロセス](#first-time-setup-wizard-process) 」を参照してください。
7. 問題の原因を特定できない場合は、 [Microsoft サポート](storsimple-8000-contact-microsoft-support.md) に対処法をお問い合わせください。 Microsoft サポートに問い合わせる際は、「 [サポート要求の作成](storsimple-8000-contact-microsoft-support.md#create-a-support-request) 」の手順に従ってください。

## <a name="cmdlets-available-for-troubleshooting"></a>トラブルシューティングに使用できるコマンドレット
接続エラーを検出するには、次の Windows PowerShell コマンドレットを使用します。

* `Get-NetAdapter`:ネットワーク インターフェイスの正常性を検出するには、このコマンドレットを使用します。
* `Test-Connection`:ネットワークの内部と外部のネットワーク接続を確認するには、このコマンドレットを使用します。
* `Test-HcsmConnection`:正常に登録されたデバイスの接続を確認するには、このコマンドレットを使用します。
* `Sync-HcsTime`:デバイスの時刻を表示し、NTP サーバーとの時間の同期を強制するにはこのコマンドレットを使用します。
* `Enable-HcsPing` および `Disable-HcsPing`:StorSimple デバイスでホストがネットワーク インターフェイスを ping を実行するにはこれらのコマンドレットを使用します。 既定では、StorSimple のネットワーク インターフェイスは ping 要求に応答しません。
* `Trace-HcsRoute`:ルート トレース ツールとしてこのコマンドレットを使用します。 最終的な宛先までにある各ルーターに長期にわたってパケットを送信し、各ホップから返されるパケットに基づく結果を計算します。 `Trace-HcsRoute` では、指定のルーターやリンクのパケット損失の程度を表示するため、ネットワークの問題の原因となっているルーターやリンクが特定できます。
* `Get-HcsRoutingTable`:ローカル IP ルーティング テーブルを表示するにはこのコマンドレットを使用します。

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Get-NetAdapter コマンドレットを使用したトラブルシューティング
デバイスを初めてデプロイするためにネットワーク インターフェイスを構成する場合は、デバイスがサービスにまだ登録されていないため、StorSimple Device Manager サービスの UI にハードウェアの状態が表示されません。 また、 **[ハードウェアの正常性]** ブレードにデバイスの状態が必ずしも正しく反映されない場合があります (特に、サービスの同期に影響する問題がある場合)。 このような場合は、 `Get-NetAdapter` コマンドレットを使用して、ネットワーク インターフェイスの正常性と状態を確認できます。

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>デバイスのすべてのネットワーク アダプターの一覧を表示するには
1. StorSimple 用 Windows PowerShell を起動し、「 `Get-NetAdapter`」と入力します。 
2. `Get-NetAdapter` コマンドレットの出力と次のガイドラインを使用して、ネットワーク インターフェイスの状態を把握します。
   
   * インターフェイスが正常な状態で、有効になっている場合、**ifIndex** の状態は **Up** と表示されます。
   * インターフェイスは正常な状態だが、(ネットワーク ケーブルで) 物理的に接続されていない場合、**ifIndex** は **Disabled** と表示されます。
   * インターフェイスは正常な状態だが、有効になっていない場合、**ifIndex** の状態は **NotPresent** と表示されます。
   * インターフェイスが存在しない場合は、この一覧に表示されません。 StorSimple Device Manager サービスの UI では、このインターフェイスの状態は失敗として今までどおり表示されます。

このコマンドレットを使用する方法の詳細については、Windows PowerShell コマンドレット リファレンスの「[Get-NetAdapter](/powershell/module/netadapter/get-netadapter)」を参照してください。

次のセクションでは、 `Get-NetAdapter` コマンドレットの出力のサンプルを示します。

 これらのサンプルでは、コントローラー 0 はパッシブ コントローラーで、次のように構成されています。

* デバイスには、DATA 0、DATA 1、DATA 2、および DATA 3 の各ネットワーク インターフェイスが存在します。
* DATA 4 と DATA 5 のネットワーク インターフェイス カードは存在しません。そのため、これらのインターフェイスは出力に表示されません。
* DATA 0 が有効になっています。

コントローラー 1 はアクティブ コントローラーで、次のように構成されています。

* デバイスには、DATA 0、DATA 1、DATA 2、DATA 3、DATA 4、および DATA 5 の各ネットワーク インターフェイスが存在します。
* DATA 0 が有効になっています。

**出力の例 – コントローラー 0**

次のサンプル データは、コントローラー 0 (パッシブ コントローラー) からの出力です。 DATA 1、DATA 2、および DATA 3 は接続されていません。 DATA 4 と DATA 5 は、デバイスに存在しないため、表示されません。

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**出力の例 – コントローラー 1**

次のサンプル データは、コントローラー 1 (アクティブ コントローラー) からの出力です。 デバイスでは、DATA 0 ネットワーク インターフェイスのみが構成され、動作しています。

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Test-Connection コマンドレットを使用したトラブルシューティング
`Test-Connection` コマンドレットを使用すると、StorSimple デバイスを外部ネットワークに接続できるかどうかを確認できます。 セットアップ ウィザードですべてのネットワーク パラメーター (DNS など) が正しく構成されている場合は、 `Test-Connection` コマンドレットを使用して、outlook.com など、ネットワークの外部の既知のアドレスに ping を実行できます。

ping コマンドレットが無効になっている場合は、接続の問題のトラブルシューティングで使用するために ping を有効にする必要があります。

`Test-Connection` コマンドレットの次の出力サンプルをご覧ください。

> [!NOTE]
> 最初のサンプルでは、デバイスで DNS が正しく構成されていません。 2 番目のサンプルでは、DNS が正しく構成されています。

**出力の例 – 正しくない DNS**

次の例には、IPv4 と IPv6 のアドレスの出力が含まれていません。これは、DNS が解決されていないことを示します。 外部ネットワークに接続されていないため、正しい DNS を指定する必要があります。

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**出力の例 – 正しい DNS**

次の例では、DNS が IPv4 アドレスを返します。これは、DNS が正しく構成されていることを示します。 この出力は、外部ネットワークに接続されていることの確認になります。

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection コマンドレットを使用したトラブルシューティング
StorSimple Device Manager サービスに既に接続され、登録されているデバイスには、`Test-HcsmConnection` コマンドレットを使用します。 このコマンドレットを使用すると、登録済みのデバイスと、対応する StorSimple Device Manager サービス間の接続を確認できます。 このコマンドは StorSimple 用 Windows PowerShell で実行できます。

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection コマンドレットを実行するには
1. デバイスが登録されていることを確認します。
2. デバイスの状態を確認します。 デバイスが非アクティブ化されているか、メンテナンス モードまたはオフラインの場合、次のいずれかのエラーが発生します。
   
   * ErrorCode.CiSDeviceDecommissioned:デバイスが非アクティブ化されていることを示します。
   * ErrorCode.DeviceNotReady:デバイスがメンテナンス モードになっていることを示します。
   * ErrorCode.DeviceNotReady:デバイスがオンラインではないことを示します。
3. ( [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) コマンドレットを使用して) StorSimple Device Manager サービスが実行されていることを確認します。 サービスが実行されていない場合、次のエラーが表示されます。
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError:Get-ClusterResource を実行したときに例外が発生したことを示します。
4. Access Control Service (ACS) トークンを確認します。 Web の例外がスローされた場合は、ゲートウェイの問題が発生した、プロキシ認証が行われなかった、DNS が正しくない、認証に失敗した、などの原因が考えられます。 次のエラーが表示されることがあります。
   
   * ErrorCode.CiSApplianceGateway:HttpStatusCode.BadGateway 例外を示します。名前解決サービスがホスト名を解決できませんでした。
   * ErrorCode.CiSApplianceProxy:HttpStatusCode.ProxyAuthenticationRequired 例外 (HTTP 状態コード 407) を示します。クライアントがプロキシ サーバーで認証できませんでした。
   * ErrorCode.CiSApplianceDNSError:WebExceptionStatus.NameResolutionFailure 例外を示します。名前解決サービスがホスト名を解決できませんでした。
   * ErrorCode.CiSApplianceACSError:サービスから認証エラーが返されたが、接続されていることを示します。
     
     Web の例外がスローされなかった場合は、ErrorCode.CiSApplianceFailure を確認します。これは、アプライアンスが失敗したことを示します。
5. クラウド サービスの接続を確認します。 サービスによって Web の例外がスローされた場合は、次のエラーが表示されることがあります。
   
   * ErrorCode.CiSApplianceGateway:HttpStatusCode.BadGateway 例外を示します。中間プロキシ サーバーが別のプロキシまたは元のサーバーから正しくない要求を受け取りました。
   * ErrorCode.CiSApplianceProxy:HttpStatusCode.ProxyAuthenticationRequired 例外 (HTTP 状態コード 407) を示します。クライアントがプロキシ サーバーで認証できませんでした。
   * ErrorCode.CiSApplianceDNSError:WebExceptionStatus.NameResolutionFailure 例外を示します。名前解決サービスがホスト名を解決できませんでした。
   * ErrorCode.CiSApplianceACSError:サービスから認証エラーが返されたが、接続されていることを示します。
     
     Web の例外がスローされなかった場合は、ErrorCode.CiSApplianceSaasServiceError を確認します。これは、StorSimple デバイス マネージャー サービスに問題があることを示します。
6. Azure Service Bus の接続を確認します。 ErrorCode.CiSApplianceServiceBusError は、デバイスが Service Bus に接続できないことを示します。

ログ ファイルの CiSCommandletLog0Curr.errlog と CiSAgentsvc0Curr.errlog には、例外の詳細など、詳しい情報が含まれています。

このコマンドレットを使用する方法の詳細については、Windows PowerShell のリファレンス ドキュメントの「 [Test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) 」をご覧ください。

> [!IMPORTANT]
> このコマンドレットは、アクティブとパッシブの両方のコントローラーに対して実行できます。

`Test-HcsmConnection` コマンドレットの次の出力サンプルをご覧ください。

**サンプル出力 - StorSimple Update 3 を実行している正常に登録済みのデバイス**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**出力の例 – オフライン デバイス** 

このサンプルは、Azure ポータルでの状態が **オフライン** のデバイスの出力です。

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

デバイスは現在の Web プロキシ構成では接続できませんでした。 Web プロキシ構成またはネットワーク接続の問題が考えられます。 この場合は、Web プロキシの設定が正しく、Web プロキシ サーバーがオンラインになっており、アクセスできることを確認する必要があります。

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Sync-HcsTime コマンドレットを使用したトラブルシューティング
デバイスの時刻を表示するには、このコマンドレットを使用します。 デバイスの時刻が NTP サーバーでオフセットになる場合、このコマンドレットを使用して時間を NTP サーバーと強制同期します。
- NTP サーバーとデバイス間のオフセットが 5 分以上の場合は、警告が表示されます。 
- オフセットが 15 分を超えている場合、デバイスはオフラインになります。 それでもこのコマンドレットを使用して時間を強制同期できます。 
- ただし、オフセットが 15 時間を超える場合、時間を強制同期することはできず、エラー メッセージが表示されます。

**サンプル出力 – Sync-HcsTime を使用した時間の強制同期**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Enable-HcsPing と Disable-HcsPing コマンドレットを使用したトラブルシューティング
デバイス上のネットワーク インターフェイスが ICMP ping 要求に応答することを確認するには、これらのコマンドレットを使用します。 既定では、StorSimple のネットワーク インターフェイスは ping 要求に応答しません。 このコマンドレットを使用すると、最も簡単な方法でデバイスがオンラインかどうか、到達可能かどうかを確認できます。

**サンプル出力 – Enable-HcsPing と Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Trace-HcsRoute コマンドレットを使用したトラブルシューティング
ルート トレース ツールとしてこのコマンドレットを使用します。 最終的な宛先までにある各ルーターに長期にわたってパケットを送信し、各ホップから返されるパケットに基づく結果を計算します。 コマンドレットでは指定のルーターやリンクのパケット損失の程度を表示するため、ネットワークの問題の原因となっているルーターやリンクが特定できます。

**Trace-HcsRoute でパケットのルートをトレースする方法を示すサンプル出力**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Get-HcsRoutingTable コマンドレットを使用したトラブルシューティング
StorSimple デバイスのルーティング テーブルを表示するには、このコマンドレットを使用します。 ルーティング テーブルは、インターネット プロトコル (IP) ネットワーク上を流れるデータ パケットの宛先の特定に役立つ一連のルールです。

ルーティング テーブルでは、インターフェイスとデータを指定したネットワークにルーティングするゲートウェイが表示されます。 また、これは特定の送信先に到達するために使用するパスの意思決定者となるルーティング メトリックも提供します。 ルーティング メトリックが低いと、優先順位が高くなります。

たとえば、2 つのネットワーク インターフェイス、DATA 2、DATA 3 をインターネットに接続しているとします。 DATA 2 と DATA 3 のルーティング メトリックがそれぞれ 15 と 261 である場合、下位のルーティング メトリックの DATA 2 は、インターネットに接続するために使用される優先インターフェイスとなります。

StorSimple デバイスで Update 1 を実行している場合は、DATA 0 のネットワーク インターフェイスがクラウド トラフィックで最優先されます。 この構成では、クラウド対応インターフェイスがほかにある場合でも、ほとんどのクラウド トラフィックが DATA 0 を経由してルーティングされます。<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

パラメーターを指定しないで `Get-HcsRoutingTable` コマンドレットを実行する場合 (次の例を参照)、コマンドレットでは IPv4 と IPv6 両方のルーティング テーブルを出力します。 または `Get-HcsRoutingTable -IPv4` や `Get-HcsRoutingTable -IPv6` を指定して関連するルーティング テーブルを取得できます。

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>StorSimple のステップ バイ ステップのトラブルシューティングの例
次の例では、StorSimple デプロイメントのステップ バイ ステップのトラブルシューティングを示します。 この例では、ネットワーク設定または DNS 名に誤りがあるという内容のエラー メッセージが表示されてデバイスの登録に失敗する、というシナリオを取り上げています。

表示されるエラー メッセージは次のとおりです。

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

エラーの原因として、次のいずれかの問題が考えられます。

* ハードウェアの設置が不適切
* ネットワーク インターフェイスの不具合
* IP アドレス、サブネット マスク、ゲートウェイ、プライマリ DNS サーバー、Web プロキシのいずれかが正しくない
* 登録キーが正しくない
* 不適切なファイアウォール設定

### <a name="to-locate-and-fix-the-device-registration-problem"></a>デバイス登録問題を見つけ、修正するには
1. デバイス構成を確認します。アクティブ コントローラーで `Invoke-HcsSetupWizard` を実行します。
   
   > [!NOTE]
   > セットアップ ウィザードはアクティブ コントローラー上で実行する必要があります。 アクティブ コントローラーに接続されているかどうかは、シリアル コンソールに表示されるバナーを見て確認できます。 接続先がコントローラー 0 であるかコントローラー 1 であるか、また、コントローラーがアクティブであるかパッシブであるかがバナーに表示されます。 詳細については、「 [デバイスでのアクティブなコントローラーの識別](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)」を参照してください。
   
2. デバイスが正しく配線されていることを確認します。デバイスのバック プレーンでネットワークのケーブル配線を確認します。 ケーブル配線はデバイスのモデルによって異なります。 詳細については、「[StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md)」または「[StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)」をご覧ください。
   
   > [!NOTE]
   > 10 GbE ネットワーク ポートを使用する場合は、提供されている QSFP-SFP アダプターと SFP ケーブルを使用する必要があります。 詳細については、[10 GbE ポートの推奨のケーブル、スイッチ、トランシーバーの一覧](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)を参照してください。
  
3. ネットワーク インターフェイスの状態を確認します。
   
   * Get-NetAdapter コマンドレットを使用し、DATA 0 のネットワーク インターフェイスの状態を検出します。 
   * リンクが正常に動作していない場合、インターフェイスがダウンしていることが `ifindex` ステータスによって示されます。 その場合は、ポートとアプライアンス間およびポートとスイッチ間のネットワーク接続を確認する必要があります。 ケーブルに不具合がないかも十分に確認してください。 
   * アクティブ コントローラーの DATA 0 ポートの不具合が疑われる場合、コントローラー 1 の DATA 0 ポートに接続することで確認できます。 デバイスの背面のコントローラー 0 からネットワーク ケーブルを取り外し、コントローラー 1 に接続してから再度、Get-NetAdapter コマンドレットを実行します。
   
     コントローラーの DATA 0 ポートに不具合がある場合は、[Microsoft サポート](storsimple-8000-contact-microsoft-support.md)に対処法をお問い合わせください。 場合によっては、ご使用のシステム上のコントローラーを交換する必要があります。
4. スイッチとの接続を確認します。
   
   * 主エンクロージャのコントローラー 0 の DATA 0 ネットワーク インターフェイスとコントローラー 1 の DATA 0 ネットワーク インターフェイスが同じサブネットに属していることを確認します。 
   * ハブまたはルーターを確認します。 通常、両方のコントローラーを同じハブまたは同じルーターに接続する必要があります。 
   * 接続に使用しているスイッチで、両方のコントローラーの DATA 0 が同じ vLAN に割り当てられていることを確認してください。
5. ユーザー エラーを排除します。
   
   * セットアップ ウィザードを再実行し ( **Invoke-HcsSetupWizard** を実行する)、エラーがないように値をもう一度入力します。 
   * 使用している登録キーを確認します。 StorSimple Device Manager サービスには、同じ登録キーを使用して複数のデバイスを接続できます。 「 [サービス登録キーの取得](storsimple-8000-manage-service.md#get-the-service-registration-key) 」の手順に従って、正しい登録キーを使用しているかどうかを確認してください。
     
     > [!IMPORTANT]
     > 複数のサービスを実行している場合、適切なサービスの登録キーを使用してデバイスを登録する必要があります。 デバイスを間違った StorSimple Device Manager サービスで登録している場合、[Microsoft サポートに対処法を問い合わせる](storsimple-8000-contact-microsoft-support.md)必要があります。 場合によっては、デバイスを出荷時の設定にリセットしたうえで、目的のサービスに接続する必要があります。その場合は、データが失われる可能性があります。
     > 
     > 
6. Test-Connection コマンドレットを使用して、外部のネットワークとの接続を確認します。 詳細については、「 [Test-Connection コマンドレットを使用したトラブルシューティング](#troubleshoot-with-the-test-connection-cmdlet)」を参照してください。
7. ファイアウォールによってブロックされている可能性がないか確認します。 仮想 IP (VIP)、サブネット、ゲートウェイ、DNS の設定がすべて正しいことを確認してもなお、接続の問題が解消しない場合、デバイスと外部ネットワーク間の通信がファイアウォールによってブロックされている可能性があります。 StorSimple デバイスでポート 80 と 443 が送信に使用できることを確認します。 詳細については、「 [StorSimple デバイスのネットワーク要件](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)」をご覧ください。
8. ログを確認します。 「 [トラブルシューティングに使用できるサポート パッケージとデバイス ログ](#support-packages-and-device-logs-available-for-troubleshooting)」をご覧ください。
9. 以上の手順で問題を解決できない場合は、 [Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md) ください。

## <a name="next-steps"></a>次のステップ
[診断ツールを使用した StorSimple デバイスのトラブルシューティング方法について説明します](storsimple-8000-diagnostics.md)。

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)