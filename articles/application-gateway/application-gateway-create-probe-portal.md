---
title: ポータルを使用してカスタム プローブを作成する
titleSuffix: Azure Application Gateway
description: ポータルを使用してアプリケーション ゲートウェイ用カスタム プローブを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95975957"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>ポータルを使用して Application Gateway 用カスタム プローブを作成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

この記事では、Azure portal を使用して既存のアプリケーション ゲートウェイにカスタム正常性プローブを追加します。 Azure Application Gateway では、正常性プローブを使用して、バックエンド プール内のリソースの正常性を監視します。

## <a name="before-you-begin"></a>開始する前に

Application Gateway がまだない場合は、[Application Gateway の作成](./quick-create-portal.md)に関する記事を参照して、使用する Application Gateway を作成します。

## <a name="create-probe-for-application-gateway-v2-sku"></a>Application Gateway v2 SKU のプローブの作成

プローブは、ポータルを通じて 2 段階の手順で構成されます。 最初の手順では、プローブ構成に必要な値を入力します。 2 番目の手順では、このプローブ構成を使用してバックエンドの正常性をテストし、プローブを保存します。 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>プローブのプロパティの入力

1. [Azure portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。

2. Azure Portal の [お気に入り] ウィンドウで [すべてのリソース] をクリックします。 [すべてのリソース] ブレードで Application Gateway をクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、[名前でフィルター] ボックスに「partners.contoso.net」と入力すると、 目的のアプリケーション ゲートウェイがすぐに見つかります。

3. **[正常性プローブ]** を選択し、 **[追加]** を選択して新しい正常性プローブを追加します。

   ![新しいプローブの追加][4]

4. **[正常性プローブの追加]** ページでプローブに必要な情報を入力し、完了したら **[OK]** を選択します。

   |**設定** | **Value** | **詳細**|
   |---|---|---|
   |**名前**|customProbe|この値は、ポータルでアクセス可能なプローブに付けるフレンドリ名です。|
   |**プロトコル**|HTTP または HTTPS | 正常性プローブが使用するプロトコルです。 |
   |**Host**|つまり contoso.com|この値は、アプリケーション サーバーで実行されている仮想ホストの名前 (VM ホスト名とは異なる) です。 プローブは \<protocol\>://\<host name\>:\<port\>/\<urlPath\> に送信されます|
   |**ホスト名をバックエンド HTTP 設定から選択します**|はい、いいえ|プローブ内の *host* ヘッダーを、このプローブが関連付けられている HTTP 設定のホスト名に設定します。 特に、Azure App Service などのマルチテナント バックエンドの場合に必要です。 [詳細情報](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**ポートをバックエンド HTTP 設定から選択します**| はい、いいえ|正常性プローブの *port* を、このプローブが関連付けられている HTTP 設定のポートに設定します。 [いいえ] を選択した場合は、使用するカスタム宛先ポートを入力できます |
   |**[ポート]**| 1-65535 | 正常性プローブに使用されるカスタム ポート | 
   |**パス**|/ または任意の有効なパス|カスタム プローブの完全な URL の残りの部分です。 パスは先頭が "/" である必要があります。 既定のパス http:\//contoso.com では "/" のみを使用します。 |
   |**間隔 (秒)**|30|正常性を確認するためにプローブを実行する頻度です。 30 秒未満に設定しないようにすることをお勧めします。|
   |**タイムアウト (秒)**|30|タイムアウトまでにプローブが待機する時間です。このタイムアウト期間内に正常な応答が受信されなかった場合は、プローブが「失敗」とマークされます。 タイムアウトまでの時間は、バックエンドの正常性ページが利用可能であることを確認するために HTTP 呼び出しを実行できるだけの長さである必要があります。 タイムアウト値は、このプローブ設定で使用される "間隔" 値、またはこのプローブに関連付けられる HTTP 設定の "要求タイムアウト" 値を超えることはできないことに注意してください。|
   |**異常のしきい値**|3|異常であると見なされる連続試行失敗回数です。 しきい値は 1 以上に設定できます。|
   |**プローブの一致条件を使用**|はい、いいえ|既定では、状態コードが 200 から 399 の HTTP(S) 応答は正常と見なされます。 バックエンドの応答コードまたはバックエンドの応答本文の許容範囲を変更できます。 [詳細情報](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP 設定**|ドロップダウンから選択|プローブは、ここで選択する HTTP 設定に関連付けられ、その結果、選択した HTTP 設定に関連付けられているバックエンド プールの正常性が監視されます。 選択した HTTP 設定で使用されているものと同じポートがプローブ要求に使用されます。 他のカスタム プローブに関連付けられていない HTTP 設定のみを選択できます。 <br>このプローブ構成で選択したプロトコルと同じプロトコルを使用し、"*ホスト名をバックエンド HTTP 設定から選択します*" スイッチと状態が同じ HTTP 設定のみが関連付けに使用できることに注意してください。|
   
   > [!IMPORTANT]
   > プローブでは、1 つ以上の HTTP 設定に関連付けられている場合にのみ、バックエンドの正常性が監視されます。 このプローブが関連付けられている HTTP 設定に関連付けられているバックエンド プールのバックエンド リソースが監視されます。 プローブ要求は、\<protocol\>://\<hostName\>:\<port\>/\<urlPath\> として送信されます。

### <a name="test-backend-health-with-the-probe"></a>プローブを使用してバックエンドの正常性をテストする

プローブのプロパティを入力したら、バックエンド リソースの正常性をテストして、プローブ構成が正しいことと、バックエンド リソースが想定どおりに動作していることを確認できます。

1. **[テスト]** を選択し、プローブの結果をメモします。 Application Gateway により、このプローブに使用される HTTP 設定に関連付けられているバックエンド プール内のすべてのバックエンド リソースの正常性がテストされます。 

   ![バックエンドの正常性のテスト][5]

2. 異常なバックエンド リソースがある場合は、 **[詳細]** 列を確認して、リソースの異常な状態の原因を把握します。 プローブ構成が正しくないためにリソースが異常とマークされている場合は、 **[プローブに戻る]** リンクを選択し、プローブ構成を編集します。 そうではなく、バックエンドの問題が原因でリソースが異常とマークされている場合は、バックエンド リソースに関する問題を解決してから、 **[プローブに戻る]** リンクを選択し、 **[テスト]** を選択して、バックエンドを再度テストします。

   > [!NOTE]
   > 異常なバックエンド リソースがある場合でもプローブの保存を選択することはできますが、お勧めしません。 これは、プローブによって異常であると判断されたバックエンド プールからの要求を Application Gateway がバックエンド サーバーに転送しないためです。 バックエンド プールに正常なリソースがない場合は、アプリケーションにアクセスできず、HTTP 502 エラーが発生します。

   ![プローブ結果の表示][6]

3. **[追加]** を選択してプローブを保存します。 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Application Gateway v1 SKU のプローブの作成

プローブは、ポータルを通じて 2 段階の手順で構成されます。 最初の手順で、プローブを作成します。 次の手順で、Application Gateway のバックエンド http 設定にプローブを追加します。

### <a name="create-the-probe"></a><a name="createprobe"></a>プローブの作成

1. [Azure portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。

2. Azure portal の [お気に入り] ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページでアプリケーション ゲートウェイを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、[名前でフィルター] ボックスに「partners.contoso.net」と入力すると、 目的のアプリケーション ゲートウェイがすぐに見つかります。

3. プローブを追加するには **[プローブ]** を選択し、 **[追加]** を選択します。

   ![Add Probe blade with information filled out][1]

4. **[正常性プローブの追加]** ブレードで、プローブに必要な情報を入力し、完了したら **[OK]** を選択します。

   |**設定** | **Value** | **詳細**|
   |---|---|---|
   |**名前**|customProbe|この値は、ポータルでアクセス可能なプローブに付けるフレンドリ名です。|
   |**プロトコル**|HTTP または HTTPS | 正常性プローブが使用するプロトコルです。 |
   |**Host**|つまり contoso.com|この値は、アプリケーション サーバーで実行されている仮想ホストの名前 (VM ホスト名とは異なる) です。 プローブは、(プロトコル)://(ホスト名):(httpsetting のポート)/urlPath に送信されます。  これは、Application Gateway でマルチサイトを構成する場合に適用可能です。 Application Gateway が 1 つのサイトに対して構成されている場合は、「127.0.0.1」と入力します。|
   |**ホスト名をバックエンド HTTP 設定から選択します**|はい、いいえ|プローブ内の "*host*" ヘッダーを、このプローブが関連付けられている HTTP 設定に関連付けられているバックエンド プール内のバックエンド リソースのホスト名に設定します。 特に、Azure App Service などのマルチテナント バックエンドの場合に必要です。 [詳細情報](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**パス**|/ または任意の有効なパス|カスタム プローブの完全な URL の残りの部分です。 パスは先頭が "/" である必要があります。 既定のパス http:\//contoso.com では "/" のみを使用します。 |
   |**間隔 (秒)**|30|正常性を確認するためにプローブを実行する頻度です。 30 秒未満に設定しないようにすることをお勧めします。|
   |**タイムアウト (秒)**|30|タイムアウトまでにプローブが待機する時間です。このタイムアウト期間内に正常な応答が受信されなかった場合は、プローブが「失敗」とマークされます。 タイムアウトまでの時間は、バックエンドの正常性ページが利用可能であることを確認するために HTTP 呼び出しを実行できるだけの長さである必要があります。 タイムアウト値は、このプローブ設定で使用される "間隔" 値、またはこのプローブに関連付けられる HTTP 設定の "要求タイムアウト" 値を超えることはできないことに注意してください。|
   |**異常のしきい値**|3|異常であると見なされる連続試行失敗回数です。 しきい値は 1 以上に設定できます。|
   |**プローブの一致条件を使用**|はい、いいえ|既定では、状態コードが 200 から 399 の HTTP(S) 応答は正常と見なされます。 バックエンドの応答コードまたはバックエンドの応答本文の許容範囲を変更できます。 [詳細情報](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > ホスト名はサーバー名と同じではありません。 この値は、アプリケーション サーバーで実行されている仮想ホストの名前です。 プローブは \<protocol\>://\<hostName\>:\<port from http settings\>/\<urlPath\> に送信されます

### <a name="add-probe-to-the-gateway"></a>ゲートウェイへのプローブの追加

プローブが作成されたら、ゲートウェイに追加します。 プローブの設定は、アプリケーション ゲートウェイのバックエンド http 設定で行います。

1. Application Gateway の **[HTTP 設定]** をクリックし、ウィンドウに表示される現在のバックエンド http 設定をクリックして、構成ブレードを表示します。

   ![https settings window][2]

2. **appGatewayBackEndHttpSettings** 設定ページで、 **[カスタム プローブの使用]** チェックボックスをオンにし、「[プローブの作成](#createprobe)」セクションで作成したプローブを **[カスタム プローブ]** ドロップダウンで選択します。
   完了したら、 **[保存]** をクリックし、設定を適用します。

## <a name="next-steps"></a>次のステップ

[バックエンドの正常性ビュー](./application-gateway-diagnostics.md#back-end-health)を使用して、プローブによって決定されたバックエンド リソースの正常性を確認します。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
