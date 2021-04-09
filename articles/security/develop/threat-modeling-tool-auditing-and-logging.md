---
title: 監査とログ記録 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での監査とログ記録の軽減策について説明します。 軽減策に関する情報を参照し、コード例を表示します。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 9d3f3ca7b5d4516c2ad5dc9cb19a2eaed0a8a4a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518279"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>セキュリティ フレーム:監査とログ記録 | 対応策 

| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[ソリューションにおける機密性の高いエンティティを識別し、変更の監査を実装する](#sensitive-entities)</li></ul> |
| **Web アプリケーション** | <ul><li>[監査とログ記録がアプリケーションに適用されていることを確認する](#auditing)</li><li>[ログのローテーションと分離の準備ができていることを確認する](#log-rotation)</li><li>[アプリケーションによって機密性の高いユーザー データがログに記録されないことを確認する](#log-sensitive-data)</li><li>[監査ファイルとログ ファイルでアクセス制限が行われていることを確認する](#log-restricted-access)</li><li>[ユーザー管理イベントがログ記録されていることを確認する](#user-management)</li><li>[システムに悪用防止機能が組み込まれていることを確認する](#inbuilt-defenses)</li><li>[Azure App Service の Web アプリの診断ログを有効にする](#diagnostics-logging)</li></ul> |
| **[データベース]** | <ul><li>[SQL Server でログインの監査が有効になっていることを確認する](#identify-sensitive-entities)</li><li>[Azure SQL での脅威の検出を有効にする](#threat-detection)</li></ul> |
| **Azure ストレージ** | <ul><li>[Azure Storage Analytics を使用して Azure Storage のアクセスを監査する](#analytics)</li></ul> |
| **WCF** | <ul><li>[ログ記録を十分に実装する](#sufficient-logging)</li><li>[監査のエラーの処理を十分に実装する](#audit-failure-handling)</li></ul> |
| **Web API** | <ul><li>[監査とログ記録が Web API に適用されていることを確認する](#logging-web-api)</li></ul> |
| **IoT フィールド ゲートウェイ** | <ul><li>[フィールド ゲートウェイに適切な監査とログ記録が適用されていることを確認する](#logging-field-gateway)</li></ul> |
| **IoT クラウド ゲートウェイ** | <ul><li>[クラウド ゲートウェイに適切な監査とログ記録が適用されていることを確認する](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>ソリューションにおける機密性の高いエンティティを識別し、変更の監査を実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | 機密データが含まれるソリューション内のエンティティを識別し、それらのエンティティとフィールドに対する変更の監査を実装します。 |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>監査とログ記録がアプリケーションに適用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | すべてのコンポーネントで監査とログ記録を有効にします。 監査ログでは、ユーザー コンテキストをキャプチャする必要があります。 すべての重要なイベントを識別し、それらのイベントをログに記録します。 一元化されたログ記録を実装します。 |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>ログのローテーションと分離の準備ができていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | <p>ログのローテーションとは、日付付きログ ファイルがアーカイブされるシステム管理において使用される、自動化されたプロセスです。 大規模なアプリケーションを実行するサーバーは、多くの場合、すべての要求をログに記録します。ログのローテーションは、最近のイベントの分析を継続しながらログの合計サイズを制限する方法の 1 つです。 </p><p>ログの分離とは、基本的に、OS またはアプリケーションが実行されている別のパーティションにログ ファイルを格納する必要があることを指し、サービス拒否攻撃またはアプリケーションのパフォーマンスの低下を回避するために実施されます。</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>アプリケーションによって機密性の高いユーザー データがログに記録されないことを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | <p>ユーザーからサイトに送信される機密データをログに記録していないことを確認します。 設計上の問題から発生する副次的な影響だけでなく、意図的なログ記録の有無も確認します。 機密データの例は次のとおりです。</p><ul><li>ユーザーの資格情報</li><li>社会保障番号やその他の識別情報</li><li>クレジット カード番号やその他の金融情報</li><li>健康に関する情報</li><li>暗号化された情報の復号化に使用される可能性がある秘密キーやその他のデータ</li><li>アプリケーションへのさらに効果的な攻撃に使用される可能性があるシステムまたはアプリケーションの情報</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>監査ファイルとログ ファイルでアクセス制限が行われていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | <p>ログ ファイルへのアクセス権が適切に設定されていることを確認します。 アプリケーション アカウントには書き込み専用アクセスが必要で、オペレーターとサポート担当者には必要に応じて読み取り専用アクセスが必要です。</p><p>フル アクセスが必要なのは管理者アカウントだけです。 ログ ファイルで、Windows ACL が適切に制限されていることを確認します。</p><ul><li>アプリケーション アカウントには書き込み専用アクセスが必要です。</li><li>オペレーターとサポート担当者には必要に応じて読み取り専用アクセスが必要です。</li><li>フル アクセスが必要なのは管理者だけです。</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>ユーザー管理イベントがログ記録されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | <p>アプリケーションが、ユーザーのログイン、パスワードのリセット、パスワードの変更、アカウントのロックアウト、ユーザー登録などのユーザー管理イベントの成功と失敗を監視していることを確認します。 これを利用して、疑わしい動作を検出して対処を行います。 操作データを収集することもできます。たとえば、だれがアプリケーションにアクセスしているのかを追跡します。</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>システムに悪用防止機能が組み込まれていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順**                   | <p>アプリケーションが悪用された場合にセキュリティ例外がスローされるように設定する必要があります。 例: 攻撃者が正規表現に一致しない悪意のあるコードを挿入しようとした場合に、セキュリティ例外がスローされることでシステムの悪用に気付くことができます。</p><p>たとえば、セキュリティ例外をログに記録し、次の問題に対してアクションを実行することをお勧めします。</p><ul><li>入力の検証</li><li>CSRF による違反</li><li>ブルート フォース攻撃 (リソースごとのユーザーあたりの要求数の上限値)</li><li>ファイル アップロードの違反</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Azure App Service の Web アプリの診断ログを有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | EnvironmentType - Azure |
| **参照**              | 該当なし  |
| **手順** | <p>Azure では、組み込みの診断機能により、App Service Web アプリのデバッグを容易に行うことができます。 これは、API アプリとモバイル アプリにも当てはまります。 App Service Web Apps は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。</p><p>これらは論理的に Web サーバー診断とアプリケーション診断に分けられます。</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>SQL Server でログインの監査が有効になっていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ログインの監査の構成](/sql/ssms/configure-login-auditing-sql-server-management-studio) |
| **手順** | <p>パスワード推測攻撃の検出と確認のために、データベース サーバーのログインの監査を有効にする必要があります。 失敗したログイン試行をキャプチャすることが重要です。 成功したログイン試行と失敗したログイン試行の両方をキャプチャすると、フォレンジック調査でのさらなるメリットになります。</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Azure SQL での脅威の検出を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure |
| **属性**              | SQL バージョン - V12 |
| **参照**              | [SQL Database 脅威の検出の概要](../../azure-sql/database/threat-detection-configure.md)|
| **手順** |<p>脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。 これによって提供される新しいセキュリティ階層により、異常なアクティビティに対するセキュリティ アラートが提供され、発生した潜在的な脅威にユーザーが検出して対応できるようになります。</p><p>ユーザーは、Azure SQL Database の監査を使用して疑わしいイベントを調査し、データベース内のデータに対するアクセス、侵害、または悪用の試行による結果かどうかを判断できます。</p><p>脅威の検出を使用すると、データベースに対する潜在的な脅威に簡単に対処できます。セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Azure Storage Analytics を使用して Azure Storage のアクセスを監査する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし |
| **参照**              | [Storage Analytics を使用して承認の種類を監視する](../../storage/blobs/security-recommendations.md#loggingmonitoring) |
| **手順** | <p>各ストレージ アカウントで、Azure Storage Analytics を有効にして、ログ記録を実行し、メトリック データを保存することができます。 Storage Analytics ログでは、だれかがストレージにアクセスするときに使用される認証方法などの重要な情報を確認できます。</p><p>ストレージへのアクセスを厳密に保護する場合、この方法が推奨されます。 たとえば、Blob Storage では、すべてのコンテナーを非公開に設定し、アプリケーション全体で SAS サービスの使用を実装できます。 次に、ログを定期的にチェックし、BLOB がストレージ アカウント キーを使用してアクセスされたか (セキュリティ違反が発生している可能性があります)、パブリックにすべきではない BLOB がパブリックであるかどうかを確認します。</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>ログ記録を十分に実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **手順** | <p>セキュリティ インシデントが発生した後の正確な監査証跡が欠如していると、フォレンジック調査の妨げとなる場合があります。 Windows Communication Foundation (WCF) には、成功または失敗した認証の試行回数をログ記録する機能が用意されています。</p><p>失敗した認証の試行をログ記録することで、管理者は潜在的なブルート フォース攻撃を警戒することができます。 同様に、成功した認証の試行をログ記録することで、正当なアカウントが侵害された場合に有用な監査証跡となる可能性があります。 WCF のサービス セキュリティ監査機能を有効にします。 |

### <a name="example"></a>例
監査が有効化された構成例は次のとおりです。
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>監査のエラーの処理を十分に実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **手順** | <p>開発されたソリューションは、監査ログへの書き込みに失敗した場合でも例外を生成しないように構成されているものです。 WCF が監査ログに書き込むことができない場合に例外をスローしないように構成されている場合、プログラムに失敗が通知されません。また、重要なセキュリティ イベントの監査も発生しない可能性があります。</p>|

### <a name="example"></a>例
次の WCF 構成ファイルの `<behavior/>` 要素は、WCF が監査ログへの書き込みに失敗した場合に、アプリケーションへの通知を行わないよう WCF に指示しています。
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
監査ログに書き込むことができない場合に、必ずプログラムに通知を行うように WCF を構成してください。 プログラムには、監査証跡が保持されていないことを組織に警告するための代替通知スキーマを用意しておく必要があります。 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>監査とログ記録が Web API に適用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | Web API で監査とログ記録を有効にします。 監査ログでは、ユーザー コンテキストをキャプチャする必要があります。 すべての重要なイベントを識別し、それらのイベントをログに記録します。 一元化されたログ記録を実装します。 |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>フィールド ゲートウェイに適切な監査とログ記録が適用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>複数のデバイスがフィールド ゲートウェイに接続するときは、個々のデバイスの接続の試行と認証状態 (成功または失敗) がログに記録され、フィールド ゲートウェイに保持されるようにします。</p><p>また、フィールド ゲートウェイによって個々のデバイスの IoT Hub 資格情報が保持されている場合は、これらの資格情報が取得されたときに監査が実行されていることを確認します。長期保存用の Azure IoT Hub またはストレージに定期的にログをアップロードするプロセスを設けてください。</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>クラウド ゲートウェイに適切な監査とログ記録が適用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IoT Hub 操作の監視の概要](../../iot-hub/iot-hub-operations-monitoring.md) |
| **手順** | <p>IoT Hub 操作の監視を通じて収集された監査データを収集および格納できるように設計します。 次の監視のカテゴリを有効にします。</p><ul><li>デバイス ID の操作</li><li>デバイスからクラウドへの通信</li><li>クラウドからデバイスへの通信</li><li>接続</li><li>ファイルのアップロード</li></ul>|