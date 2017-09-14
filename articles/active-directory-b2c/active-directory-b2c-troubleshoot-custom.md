---
title: "Application Insights を使用したカスタム ポリシーのトラブルシューティング - Azure AD B2C | Microsoft Docs"
description: "カスタム ポリシーの実行を追跡するための Application Insights の設定方法"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: saeda
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 8c79df33cd5f04f490e2cc6372f7e8ac1c4d9bbe
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: ログの収集

この記事では、カスタム ポリシーの問題を診断できるよう、Azure AD B2C からログを収集する手順を説明します。

>[!NOTE]
>現在、ここで説明する詳細なアクティビティ ログは、カスタム ポリシーの開発**のみ**を支援するように設計されています。 実稼働環境では開発モードを使わないでください。  ログは、開発中に ID プロバイダーとの間で送受信されるすべての要求を収集します。  実稼働環境で使う場合、開発者は、自分が所有する App Insights ログに PII (個人を特定できる情報) が収集されることに対して責任を追うことになります。  これらの詳細ログは、ポリシーが**開発モード**で配置されている場合にのみ収集されます。


## <a name="use-application-insights"></a>Application Insights を使用する

Azure AD B2C では、Application Insights にデータを送信するための機能がサポートされています。  Application Insights は、例外を診断したり、アプリケーションのパフォーマンスの問題を視覚化したりするための手段を提供します。

### <a name="setup-application-insights"></a>Application Insights を設定する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 (Azure AD B2C テナントではなく) Azure サブスクリプションがあるテナントにいることを確認します。
1. 左側のナビゲーション メニューで、**[+ New]**(+ 新規) をクリックします。
1. **Application Insights** を探して選択し、**[Create]**(作成) をクリックします。
1. フォームに入力して **[Create]**(作成) をクリックします。 **[Application Type]**(アプリケーションの種類) は **[General]**(一般) を選択します。
1. リソースが作成されたら、Application Insights リソースを開きます。
1. 左側のメニューから **[Properties]**(プロパティ) を探してクリックします。
1. **インストルメンテーション キー**をコピーし、次のセクションのために保存します。

### <a name="set-up-the-custom-policy"></a>カスタム ポリシーを設定する

1. RP ファイル (例: SignUpOrSignin.xml) を開きます。
1. 次の属性を `<TrustFrameworkPolicy>` 要素に追加します:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. まだ存在しない場合、子ノード `<UserJourneyBehaviors>` を `<RelyingParty>` ノードに追加します。 `<DefaultUserJourney ReferenceId="YourPolicyName" />` の直後に配置する必要があります。
2. 次のノードを `<UserJourneyBehaviors>` 要素の子として追加します。 `{Your Application Insights Key}` の部分は必ず、前のセクションで Application Insights から取得した**インストルメンテーション キー**に置き換えてください。

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` は、開発に適するが高ボリューム時は制約を受ける処理パイプラインを通じてテレメトリを迅速化するよう、Application Insights に指示します。
  * `ClientEnabled="true"` は、ページ ビューとクライアント側エラーを追跡するためのクライアント側スクリプトを Application Insights に送信します (不要)。
  * `ServerEnabled="true"` は、既存の UserJourneyRecorder JSON をカスタム イベントとして Application Insights に送信します。
サンプル:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. ポリシーをアップロードします。

### <a name="see-the-logs-in-application-insights"></a>Application Insights でログを表示する

>[!NOTE]
> Application Insights で新しいログが確認できるようになるまで、少し時間がかかります (5 分以内)。

1. [Azure Portal](https://portal.azure.com) で作成した Application Insights リソースを開きます。
1. **[Overview]**(概要) メニューで、**[Analytics]**(分析) をクリックします。
1. Application Insights で新しいタブを開きます。
1. 次に示すのは、ログを確認するために使用できるクエリの一覧です

| クエリ | 説明 |
|---------------------|--------------------|
traces | Azure AD B2C によって生成されたすべてのログを確認します |
traces \| where timestamp > ago(1d) | Azure AD B2C によって生成された直近 1 日分のすべてのログを確認します

エントリは長い可能性があります。  詳細を確認する際は CSV にエクスポートしてください。

分析ツールの詳細については、[こちら](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)を参照してください。

>[!NOTE]
>ID 開発者を支援するためのユーザー体験ビューアーがコミュニティによって開発されています。  Microsoft によってサポートされていないこのビューアーは、厳密に現状のまま利用可能です。  このビューアーは、Application Insights インスタンスからデータを読み取り、ユーザー体験イベントの適切に構造化された表示を提供します。  ソース コードを入手し、独自のソリューションでデプロイできます。

>[!NOTE]
>現在、ここで説明する詳細なアクティビティ ログは、カスタム ポリシーの開発**のみ**を支援するように設計されています。 実稼働環境では開発モードを使わないでください。  ログは、開発中に ID プロバイダーとの間で送受信されるすべての要求を収集します。  実稼働環境で使う場合、開発者は、自分が所有する App Insights ログに PII (個人を特定できる情報) が収集されることに対して責任を追うことになります。  これらの詳細ログは、ポリシーが**開発モード**で配置されている場合にのみ収集されます。

[サポートされていないカスタム ポリシーのサンプルと関連ツールの GitHub リポジトリ](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>次のステップ

Application Insights でデータを探索すると、B2C の基盤となる Identity Experience Framework が独自の ID エクスペリエンスを提供するためにどのように機能するかを理解できます。

