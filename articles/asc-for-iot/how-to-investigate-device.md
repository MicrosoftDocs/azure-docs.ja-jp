---
title: Azure Security Center for IoT デバイス調査ガイド (プレビュー) | Microsoft Docs
description: この攻略ガイドでは、Log Analytics を使用して疑わしい IoT デバイスを調査するために Azure Security Center for IoT を使用する方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 884d001a65962d5e7e6e52dd47ce6ad7e02e1057
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618113"
---
# <a name="investigate-a-suspicious-iot-device"></a>疑わしい IoT デバイスを調査する

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Security Center (ASC) for IoT サービスのアラートと証拠では、IoT デバイスが不審なアクティビティに関与していると疑われる場合、またはデバイスが侵害されている兆候が存在する場合に、それが明確に通知されます。 

このガイドでは、組織に対する潜在的なリスクの特定、修復方法の決定、および今後の同様の攻撃を防ぐための最善の方法の発見を支援するために提供される、調査の提案を使用します。  

> [!div class="checklist"]
> * デバイス データを探す
> * kql クエリを使用して調査する


## <a name="how-can-i-access-my-data"></a>自分のデータにアクセスする方法

既定では、ASC for IoT は、セキュリティ アラートとレコメンデーションを Log Analytics ワークスペースに保存します。 未加工のセキュリティ データを保存することもできます。

データ保存用の Log Analytics ワークスペースを見つけるには、次のように操作します。

1. IoT ハブを開きます。 
1. **[セキュリティ]** で **[概要]** をクリックし、 **[設定]** を選択します。
1. Log Analytics ワークスペースの構成の詳細を変更します。 
1. **[Save]** をクリックします。 

Log Analytics ワークスペースに保存されているデータにアクセスするには、以下の構成に対して次の操作を行います。

1. IoT Hub の ASC for IoT アラートを選択してクリックします。 
1. **[Further investigation]\(さらに調査\)** をクリックします。 
1. **[To see which devices have this alert click here and view the DeviceId column]\(このアラートがどのデバイスのものかを確認するには、ここをクリックして DeviceId 列を見てください\)** を選択します。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>疑わしい IoT デバイスに対する調査の手順

IoT デバイスについての分析情報と生データにアクセスするには、Log Analytics ワークスペースに移動して[自分のデータにアクセス](#how-can-i-access-my-data)します。

以下の kql クエリを使用して、以下の詳細およびアクティビティについてデバイス データを確認および調査します。

### <a name="related-alerts"></a>関連するアラート

他のアラートが同じころにトリガーされたかどうかを調べるには、次の kql クエリを使用します。

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>アクセスできるユーザー

このデバイスにどのユーザーがアクセスできるかを調べるには、次の kql クエリを使用します。 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
このデータを使用して、以下の事項を調べます。 
  1. どのユーザーがデバイスにアクセスできるか。
  2. アクセスできるユーザーが、望ましいアクセス許可レベルを持っているか。 

### <a name="open-ports"></a>ポートを開く

デバイスで現在使用されているポートまたは過去に使用されたポートを調べるには、次の kql クエリを使用します。 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. 現在、デバイスで、どのリッスンしているソケットがアクティブになっているか。
  2. 現在アクティブであるリッスン ソケットは許可する必要があるか。
  3. デバイスに接続されている不審なリモート アドレスがあるか。

### <a name="user-logins"></a>ユーザー ログイン

デバイスにログインしたユーザーを調べるには、次の kql クエリを使用します。 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. どのユーザーがデバイスにログインしたか。
  2. ログインしたユーザーはログインすると想定されていたか。
  3. ログインしたユーザーは、予期される IP アドレスから接続したか、予期されない IP アドレスから接続したか。
  
### <a name="process-list"></a>プロセス一覧

プロセス一覧は予想されているものかどうかを調べるには、次の kql クエリを使用します。 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. デバイス上で疑わしいプロセスが実行されていたか。
  2. プロセスが適切なユーザーによって実行されていたか。
  3. すべてのコマンド ラインの実行で、予期された正しい引数が指定されていたか。

## <a name="next-steps"></a>次の手順

デバイスを調査し、リスクをより深く理解した後で、IoT ソリューションのセキュリティ体制を向上させるために、[カスタム アラートの構成](quickstart-create-custom-alerts.md)の検討が必要になることがあります。 まだデバイス エージェントがない場合は、結果を改善するために、[セキュリティ エージェントのデプロイ](how-to-deploy-agent.md)または[既存のデバイス エージェントの構成の変更](how-to-agent-configuration.md)を検討してください。 