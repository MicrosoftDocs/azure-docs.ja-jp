---
title: Azure AD Connect クラウド同期のトラブルシューティング
description: この記事では、クラウド プロビジョニング エージェントで発生する可能性のある問題をトラブルシューティングする方法について説明します。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 174ec8c42ea17ccae04769d7c0baaa91b8e7025b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517873"
---
# <a name="cloud-sync-troubleshooting"></a>クラウド同期のトラブルシューティング

クラウド同期は、さまざまな項目に影響を及ぼし、さまざまな依存関係を持ちます。 この幅広い範囲により、さまざまな問題が発生する可能性があります。 この記事は、これらの問題のトラブルシューティングを行うために役立ちます。 ここでは、注目すべき一般的な領域、追加情報を収集する方法、および問題を追跡するために使用できるさまざまな手法について説明します。


## <a name="common-troubleshooting-areas"></a>一般的なトラブルシューティング分野

|名前|説明|
|-----|-----|
|[エージェントに関する問題](#agent-problems)|エージェントが正しくインストールされていること、および Azure Active Directory (Azure AD) と通信していることを確認します。|
|[オブジェクトの同期に関する問題](#object-synchronization-problems)|プロビジョニング ログを使用して、オブジェクトの同期に関する問題をトラブルシューティングします。|
|[プロビジョニングの検疫に関する問題](#provisioning-quarantined-problems)|プロビジョニングの検疫に関する問題とその解決方法を理解します。|


## <a name="agent-problems"></a>エージェントに関する問題
エージェントに関して検証する最初の項目は、次のとおりです。

-  インストール済みであるか。
-  エージェントはローカルで実行されているか。
-  エージェントはポータル内にあるか。
-  エージェントは正常とマークされているか。

これらの項目は、Azure portal のほか、エージェントが実行されているローカル サーバーで検証できます。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認

エージェントが Azure によって認識されていて、正常であることを確認するには、次の手順を実行します。

1. Azure portal にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。 中央にある **[同期の管理]** を選択します。
1. **[Azure AD Connect cloud sync]\(Azure AD Connect クラウド同期\)** 画面で、 **[すべてのエージェントの確認]** を選択します。

   ![すべてのエージェントの確認](media/how-to-install/install-7.png)</br>
 
1. **[オンプレミスのプロビジョニング エージェント]** 画面に、インストールしたエージェントが表示されます。 該当するエージェントが存在すること、また *[正常]* としてマークされていることを確認します。

   ![[オンプレミスのプロビジョニング エージェント] 画面](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>ポートを確認する

Azure がポート 443 でリッスンしていて、お使いのエージェントがそれと通信できることを確認します。 

このテストでは、エージェントがポート 443 を介して Azure と通信できることを確認します。 ブラウザーを開き、エージェントがインストールされているサーバーから前の URL に移動します。

![ポートの到達可能性の確認](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>ローカル サーバーの場合

エージェントが実行されていることを確認するには、次の手順に従います。

1. エージェントがインストールされているサーバーで **[サービス]** を開きます。これには、そこに直接移動するか、**スタート** ボタンをクリックし、 **[ファイル名を指定して実行]** で「**Services.msc**」と入力します。
1. **[サービス]** に **[Microsoft Azure AD Connect エージェント アップデーター]** と **[Microsoft Azure AD Connect プロビジョニング エージェント]** が存在すること、その状態が *[実行中]* になっていることを確認します。

   ![[サービス] 画面](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>エージェントのインストールに関する一般的な問題

以下のセクションでは、エージェントのインストールに関する一般的な問題と一般的な解決策について説明します。

#### <a name="agent-failed-to-start"></a>エージェントが起動しない

次の内容のエラー メッセージが表示されることがあります。

**Service 'Microsoft Azure AD Connect Provisioning Agent' failed to start.Verify that you have sufficient privileges to start the system. (サービス 'Microsoft Azure AD Connect Provisioning Agent' を開始できませんでした。システム サービスを起動するために十分な特権を持っていることを確認してください。)** 

この問題は、通常、インストーラー (NT SERVICE\AADConnectProvisioningAgent) によって作成されたローカル NT サービスのログオン アカウントにアクセス許可が適用されないようにするグループ ポリシーが原因で発生します。 サービスを開始するには、これらのアクセス許可が必要です。

この問題を解決するには、次の手順に従ってください。

1. 管理者アカウントでサーバーにサインインします。
1. **[サービス]** を開きます。これには、そこに直接移動するか、**スタート** ボタンをクリックし、 **[ファイル名を指定して実行]** で「**Services.msc**」と入力します。
1. **[サービス]** で **[Microsoft Azure AD Connect プロビジョニング エージェント]** をダブルクリックします。
1. **[ログオン]** タブで、 **[このアカウント]** をドメイン管理者に変更します。その後、サービスを再起動します。 

   ![[ログオン] タブ](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>エージェントがタイムアウトになるか証明書が無効である

エージェントを登録しようとしたときに、次のエラー メッセージが表示されることがあります。

![タイムアウト エラー メッセージ](media/how-to-troubleshoot/troubleshoot-4.png)

この問題は、通常、エージェントがハイブリッド ID サービスに接続できないことが原因で発生し、HTTP プロキシを構成する必要があります。 この問題を解決するには、送信プロキシを構成します。 

プロビジョニング エージェントでは、送信プロキシの使用がサポートされます。 構成するには、エージェントの構成ファイル *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* を編集します。ファイルの末尾近くにある `</configuration>` の終了タグの直前に次の行を追加します。
`[proxy-server]` 変数と `[proxy-port]` 変数をお使いのプロキシ サーバー名とポート値に置き換えてください。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>セキュリティ エラーが発生してエージェントの登録が失敗する

クラウド プロビジョニング エージェントのインストール時にエラー メッセージが表示されることがあります。

この問題は、通常、ローカルの PowerShell 実行ポリシーのせいで、エージェントで PowerShell 登録スクリプトを実行できないことが原因で発生します。

この問題を解決するには、サーバー上の PowerShell 実行ポリシーを変更します。 マシンとユーザーのポリシーを *Undefined* または *RemoteSigned* に設定する必要があります。 *Unrestricted* として設定されている場合、このエラーが表示されます。 詳細については、[PowerShell 実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)に関する記事を参照してください。 

### <a name="log-files"></a>ログ ファイル

既定では、エージェントによって発行されるエラー メッセージとスタック トレース情報は最小限に抑えられています。 これらのトレース ログは、**C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace** フォルダーにあります。

エージェント関連の問題をトラブルシューティングするための追加の詳細情報を収集するには、次の手順に従います。

1.  [こちら](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)の説明に従って、AADCloudSyncTools PowerShell モジュールをインストールします。
2. `Export-AADCloudSyncToolsLogs` PowerShell コマンドレットを使用して情報をキャプチャします。  データ コレクションを微調整するには、次のスイッチを使用できます。
      - SkipVerboseTrace: 詳細ログをキャプチャせずに現在のログのみをエクスポートします (既定値 = false)
      - TracingDurationMins: 別のキャプチャ期間を指定します (既定値 = 3 分)
      - OutputPath: 別の出力パスを指定します (既定値 = ユーザーのドキュメント)


## <a name="object-synchronization-problems"></a>オブジェクトの同期に関する問題

次のセクションでは、オブジェクトの同期に関するトラブルシューティングについて説明します。

### <a name="provisioning-logs"></a>プロビジョニング ログ

Azure portal では、プロビジョニング ログを使用して、オブジェクトの同期に関する問題の追跡とトラブルシューティングを行うことができます。 ログを表示するには、 **[ログ]** を選択します。

![[ログ] ボタン](media/how-to-troubleshoot/log-1.png)

プロビジョニング ログは、オンプレミスの Active Directory 環境と Azure の間で同期されているオブジェクトの状態に関するさまざまな情報を提供します。

![[プロビジョニング ログ] 画面](media/how-to-troubleshoot/log-2.png)

ページ上部にあるドロップダウン ボックスを使用して、特定の問題に関するビューを日付などでフィルター処理できます。 個々のイベントをダブルクリックすると、追加情報が表示されます。

![[プロビジョニング ログ] ドロップダウン ボックスの情報](media/how-to-troubleshoot/log-3.png)

この情報は、詳しい手順と、同期に関する問題が発生している場所を示しています。 この方法で、問題の正確な場所を特定できます。


## <a name="provisioning-quarantined-problems"></a>プロビジョニングの検疫に関する問題

クラウド同期では、構成の正常性が監視され、正常でないオブジェクトは検疫状態に置かれます。 ターゲット システムに対する呼び出しのほとんどまたはすべてが、管理者の資格情報が無効であるなどの理由で常にエラーで失敗する場合、同期ジョブは検疫状態になります。

![検疫の状態](media/how-to-troubleshoot/quarantine-1.png)

この状態を選択することで、検疫に関する追加情報を表示できます。 エラー コードとメッセージも取得できます。

![検疫に関する詳細を示すスクリーンショット。](media/how-to-troubleshoot/quarantine-2.png)

状態を右クリックすると、追加のオプションが表示されます。
    
   - プロビジョニング ログの表示
   - エージェントの表示
   - 検疫のクリア

![右クリック メニュー オプションを示すスクリーンショット。](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>検疫を解決する
検疫を解決するには、2 つの異なる方法があります。  これらは次のとおりです。

  - 検疫のクリア - 透かしをクリアし、差分同期を実行します
  - プロビジョニング ジョブを再起動する - 透かしをクリアし、初期同期を実行します

#### <a name="clear-quarantine"></a>検疫のクリア
透かしをクリアし、確認後にプロビジョニング ジョブで差分同期を実行するには、状態を右クリックし、 **[clear quarantine]\(検疫のクリア\)** を選択します。

検疫がクリアされていることを示す通知が表示されます。

![検疫がクリアされていることを示すスクリーンショット。](media/how-to-troubleshoot/quarantine-5.png)

これで、エージェントの状態が正常であることを確認できます。

![検疫状態の情報](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>プロビジョニング ジョブを再起動する
Azure portal を使用して、プロビジョニング ジョブを再起動します。 エージェントの構成ページで **[プロビジョニングを再開する]** を選択します。

  ![プロビジョニングを再開する](media/how-to-troubleshoot/quarantine-3.png)

- Microsoft Graph を使用して、[プロビジョニング ジョブを再起動します](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 再起動する対象は完全に制御できます。 次のものをクリアすることを選択できます。
  - エスクロー。検疫状態を発生させるエスクロー カウンターを再起動します。
  - 検疫。アプリケーションを検疫から削除します。
  - ウォーターマーク。 
  
  次の要求を使用します。
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>クラウド同期サービス アカウントの修復
クラウド同期サービス アカウントを修復する必要がある場合は、`Repair-AADCloudSyncToolsAccount` を使用できます。  


   1.  [こちら](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)に記載されているインストール手順を使用して開始し、残りの手順を続行します。
   2.  管理者特権を持つ Windows PowerShell セッションから、次を入力するかコピーして貼り付けます。 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. ご自分の Azure AD グローバル管理者の資格情報を入力します。
   4. 次を入力するか、コピーして貼り付けます。 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. これを完了すると、アカウントが正常に修復されたことが示されます。

## <a name="next-steps"></a>次のステップ 

- [既知の制限事項](how-to-prerequisites.md#known-limitations)
- [エラー コード](reference-error-codes.md)
