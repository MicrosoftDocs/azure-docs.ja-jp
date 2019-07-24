---
title: リモート監視内の電子メール アクション - Azure | Microsoft Docs
description: この攻略ガイドでは、新規または既存の電子ルールにメール アクションを追加する方法について説明します。
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 693da06ce14a4854d5db49f588fa29d791060166
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108105"
---
# <a name="add-an-email-action"></a>電子メール アクションを作成する

電子メール アクションを使用すると、アラートの見落としを防ぐことができます。 電子メール アクションは、既存のルールに追加することも、新規ルールの作成時に追加することもできます。

この攻略ガイドの手順を実行するには、Azure サブスクリプション内でデプロイした、リモート監視ソリューション アクセラレータのインスタンスが必要です。

ルールを作成または変更するには、[**管理者**であること、または適切な権限を所有していること](iot-accelerators-remote-monitoring-rbac.md)が必要です。

## <a name="edit-an-existing-rule"></a>既存のルールを編集する

メール アクションを既存のルールに追加するには、次の手順を実行します。

1. リモート監視ソリューションに移動します。

1. **ダッシュボード**から **[ルール]** ページに移動します。

    ![[ルール] ページ](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. 変更する既存のルールの横にあるチェック ボックスをクリックし、上部にある **[編集]** をクリックします。 編集可能な **[ルール]** パネルが表示されます。

1. **[アクション]** セクションで、**[Email enabled]\(電子メールが有効\)** を **[On]\(オン\)** に切り替えます。

1. 最初にソリューション アクセラレータで電子メール アクションを有効にするときは、[Outlook にサインイン](#outlook)する必要があります。

1. 追加する電子メール アドレスごとに、電子メール アドレスを [受信者] ボックスに入力して、**Enter** キーを押します。

    ![アドレスの入力](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 電子メールの件名を入力します。

1. 電子メール受信者について、その他のメモがあれば、プレーンテキストで入力します。 [電子メール テンプレートを編集](#htmledit)する場合は、HTML 形式を使用できます。

1. **[ルールの状態]** が **[有効]** に設定されていることを確認します。

1. **[Apply]** をクリックします。

## <a name="create-a-new-rule"></a>新しいルールの作成

新規ルールの作成時に電子メール アクションを追加するには、次の手順を実行します。

1. リモート監視ソリューションに移動します。

1. **ダッシュボード**から **[ルール]** ページに移動します。

    ![[ルール] ページ](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. [「create a rule」 (ルールを作成する) セクション](iot-accelerators-remote-monitoring-automate.md#create-a-rule)の手順を実行します。 [「create an advanced rule」 (高度なルールを作成する)](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) セクションの手順を、**[重大度レベル]** を設定するところまで実行します。 **[適用]** はまだクリックしないでください。

1. **[アクション]** セクションで、**[Email enabled]\(電子メールが有効\)** を **[On]\(オン\)** に切り替えます。

1. 最初にソリューション アクセラレータで電子メール アクションを有効にするときは、[Outlook にサインイン](#outlook)する必要があります。

1. 追加する電子メール アドレスごとに、電子メール アドレスを [受信者] ボックスに入力して、**Enter** キーを押します。

    ![アドレスの入力](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 電子メールの件名を入力します。

1. 電子メール受信者について、その他のメモがあれば、プレーンテキストで入力します。 [電子メール テンプレートを編集](#htmledit)する場合は、HTML 形式を使用できます。

1. **[ルールの状態]** が **[有効]** に設定されていることを確認します。

1. **[Apply]** をクリックします。

以上で電子メール アクションを含むルールが有効になります。 アクションがトリガーされるたびに、新しい電子メールが受信者に送信されます。

## Outlook にサインインする<a name="outlook"></a>

最初にソリューション アクセラレータで電子メール アクションを有効にするときは、Outlook にサインインする必要があります。 このアクションにより、電子メール通知を送信する電子メール アカウントがセットアップされます。

> [!NOTE]
> ソリューション アクセラレータの通知専用の Outlook アカウントを作成し、最初の電子メール アクションを有効にするときにそのアカウントを使用してください。

### <a name="contributor-role-outlook-setup"></a>共同作成者ロールの Outlook のセットアップ

サブスクリプション内の**共同作成者**ロールのいずれかのユーザーがソリューション アクセラレータをデプロイした場合は、アプリケーションに十分な権限がなく、Web UI を通じて電子メール アクションをセットアップおよび検証できません。

作業を始める前に、ソリューション アクセラレータから電子メール通知を送信するために使用される Outlook アカウントを作成します。

次の手順は、電子メール アクションを手動でセットアップおよび検証する方法を示しています。

1. [Azure Portal](https://portal.azure.com) に移動します。

1. ソリューション アクセラレータ用のリソース グループに移動します。

1. **[office365-connector]\(Office365 コネクタ\)** をクリックします。

    ![API 接続](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. 承認プロセスを開始するバナーをクリックします。

    ![承認](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. **[承認]** をクリックします。 サインインするように求められます。 サインインに使用するアカウントは、アプリケーションで電子メール通知を送信する際に使用する電子メール アドレスでなければなりません。

    ![Authorize button](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. 下部にある **[保存]** をクリックします。 バナーが消えた場合は、承認が成功します。

1. 通知の送信元の電子メール アドレスを変更するには、 **[API 接続の編集]** をクリックします。

    ![電子メールの変更](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>所有者ロールの Outlook のセットアップ

サブスクリプション内の**所有者**ロールのいずれかのユーザーがソリューション アクセラレータをデプロイした場合は、電子メール アクションが Web UI を通じて正しくセットアップされたかどうかをアプリケーションで検証できます。

作業を始める前に、ソリューション アクセラレータから電子メール通知を送信する際に使用される Outlook アカウントを作成します。

サインインして電子メール アクションをセットアップするには、次の手順を実行します。

1. [sign in to Outlook]\(Outlook にサインインする\) をクリックします。 Azure portal が表示されます。

   ![Outlook にサインインする](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. **[承認]** をクリックします。 サインインするように求められます。 サインインに使用するアカウントは、アプリケーションで電子メール通知を送信する際に使用する電子メール アドレスでなければなりません。

1. **[Save]** をクリックします。 ソリューション アクセラレータに戻り、ページを更新します。

1. 電子メール通知が正常に構成されると、次のメッセージが表示されます。

   ![Outlook へのサインインが成功](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## 電子メールの HTML をカスタマイズする<a name="htmledit"></a>

リモート監視ソリューション アクセラレータには、アクション電子メール用のすぐに使用できる基本的な HTML テンプレートが用意されています。 電子メール テンプレートでは、電子メール アクション設定からの値が使用されます。 電子メールの例を次に示します。

![電子メールの例](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

次の手順は、HTML 電子メール テンプレートの編集方法を示しています。 たとえば、より多くの情報を含めたり、カスタム イメージを追加したりすることが可能です。

1. Java または .NET リモート監視 GitHub リポジトリを複製します。

1. 電子メール テンプレートの場所に移動します。
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. このテンプレートでパラメーターを追加したり、削除したりして、メッセージをカスタマイズできます。 必要に応じて、呼び出しを追加、削除、置換することもできます。

    .NET コードの場合の例を次に示します。 `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Java コードの場合の例を次に示します。 `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. テンプレート内のパラメーターの形式は、`${...}` となります。 パラメーターを削除するには、必要な行を削除します。 パラメーターを追加するには、行を追加して、挿入する値を指定します。

1. イメージまたはカスタム テキストを追加するには、EmailTemplate.HTML ファイルを直接更新します。

## <a name="throttling"></a>Throttling

リモート監視ソリューション アクセラレータでは、Outlook を使用して、電子メール通知が送信されます。 Outlook では、送信する電子メール数が [1 分あたり 30 通](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)に制限されています。 電子メールを受信する電子メール クライアントでは、1 分あたりに受信する電子メール数を調整することもできます。 制限については、電子メール クライアントを確認してください。 ルールの電子メール通知をセットアップする場合、ルールでは、1分以上の期間の平均値を計算する必要があります。瞬時値は使用しないでください。

![平均の計算](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>次の手順

このガイドでは、リモート監視ソリューション内で電子メール アクションを新規または既存のルールに追加する方法を説明しました。 また、メッセージの書式を定義する HTML の編集方法も説明しました。

次の手順として、[アラートの使用方法とデバイスの問題の解決方法](iot-accelerators-remote-monitoring-maintain.md)を学習することをお勧めします。
