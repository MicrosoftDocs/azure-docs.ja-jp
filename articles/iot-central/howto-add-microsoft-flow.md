---
title: Microsoft Flow 内で Azure IoT Central コネクタを使用してワークフローを作成する | Microsoft Docs
description: Microsoft Flow 内で IoT Central コネクタを使用して、ワークフローのトリガーや、ワークフロー内のデバイスの作成、取得、更新、削除、コマンドの実行を行います。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: e8bc8b8d4e3585ea4c0505f2e36abc6d1da7f8eb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797721"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Microsoft Flow 内で IoT Central コネクタを使用してワークフローを作成する

*このトピックはビルダーおよび管理者向けです。*

Microsoft Flow を使用すると、ビジネス ユーザーが利用する多くのアプリケーションやサービスにまたがるワークフローを自動化できます。 Microsoft Flow 内で IoT Central コネクタを使用すると、IoT Central 内で規則がトリガーされたときにワークフローをトリガーできます。 IoT Central やその他のアプリケーションによってトリガーされたワークフロー内では、IoT Central コネクタ内のアクションを使用して、次のことを行うことができます。
- デバイスを作成する
- デバイス情報を取得する
- デバイスのプロパティと設定を更新する
- デバイスでコマンドを実行する
- デバイスを削除する

IoT Central をモバイル通知や Microsoft Teams などの他のサービスに接続する[これらの Microsoft Flow テンプレート](https://aka.ms/iotcentralflowtemplates)をチェックアウトしてください。

## <a name="prerequisites"></a>前提条件

- 従量課金制アプリケーション
- Microsoft Flow を使用するための、Microsoft の個人アカウント、または職場または学校アカウント ([Microsoft Flow のプランの詳細を参照してください](https://aka.ms/microsoftflowplans))
- Azure IoT Central コネクタを使用するための職場または学校アカウント

## <a name="trigger-a-workflow"></a>ワークフローのトリガー

このセクションでは、IoT Central 内で規則がトリガーしたときに Flow モバイル アプリでモバイル通知をトリガーする方法について説明します。 組み込みの Microsoft Flow デザイナーを使用して IoT Central アプリ内でこのワークフロー全体を構築することができます。

1. 最初に、[IoT Central 内で規則を作成](howto-create-telemetry-rules.md)します。 規則の条件を保存したら、新しいアクションとして **[Microsoft Flow action]\(Microsoft Flow アクション\)** を選択します。 ワークフローを構成するためのダイアログ ウィンドウが開きます。 サインインしている IoT Central のユーザー アカウントは、Microsoft Flow へのサインインに使用されます。

    ![新しい Microsoft Flow のアクションを作成する](media/howto-add-microsoft-flow/createflowaction.png)

1. アクセスが可能で、この IoT Central 規則に付属しているワークフローのリストが表示されます。 **[テンプレートを探す]** または **[新規] > [テンプレートから作成]** をクリックすると、使用可能ないずれかのテンプレートを選択できます。 

    ![使用可能な Microsoft Flow テンプレート](media/howto-add-microsoft-flow/flowtemplates1.png)

1. 選択したテンプレートのコネクタにサインインするように求められます。 

    > [!NOTE]
    > Azure IoT Central コネクタを使用するには、Azure Active Directory アカウント (職場または学校アカウント) を使用してサインインする必要があります。 abc@outlook.com や abc@live.com などの個人アカウントは、Azure IoT Central コネクタではサポートされていません。

    コネクタにサインインすると、ワークフローを構築するためのデザイナーが表示されます。 ワークフローには、[アプリケーション] と [規則] が既に入力されている IoT Central トリガーが含まれています。

1. アクションに渡される情報をカスタマイズしたり、新しいアクションを追加したりすることで、ワークフローをカスタマイズできます。 この例では、アクションは **[通知] - [Send me a mobile notification]\(モバイル通知を受け取る\)** です。 IoT Central 規則からの"*動的なコンテンツ*"を含め、デバイス名やタイムスタンプなどの重要な情報を通知に渡すことができます。

    > [!NOTE]
    > [動的なコンテンツ] ウィンドウ内の **[See more]** \(もっと見る\) のテキストを選択し、規則をトリガーした測定値とプロパティ値を取得します。

    ![動的なウィンドウを開いて Flow でアクションを編集します](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. アクションの編集が完了したら、 **[保存]** を選択します。 ワークフローの概要ページが表示されます。 ここで、実行履歴を表示して、他の同僚と共有できます。

    > [!NOTE]
    > IoT Central アプリでこの規則を他のユーザーに編集してもらいたい場合は、Microsoft Flow 内でこの規則を他のユーザーと共有します。 ワークフロー内で他のユーザーの Microsoft Flow アカウントを所有者として追加します。

1. IoT Central アプリに戻ると、この規則の [Actions]\(アクション\) 区分に Microsoft Flow アクションが表示されます。

Microsoft Flow から直接 IoT Central コネクタを使用してワークフローを構築することもできます。 これで、接続先の IoT Central アプリを選択できるようになります。

## <a name="create-a-device-in-a-workflow"></a>ワークフロー内のデバイスを作成する

このセクションでは、Microsoft Flow モバイル アプリを使い、モバイル デバイスのボタンを押して IoT Central 内に新しいデバイスを作成する方法について説明します。 Flow 内でこのアクションを使用して、Dynamics などの ERP システムと IoT Central を統合できます。これを行うには、デバイスが他の場所で追加されたときに、新しいデバイスを作成する必要があります。

1. 最初に Microsoft Flow 内で空のワークフローを作成します。

1. トリガーとして **[Flow button for mobile]\(モバイル用の Flow ボタン\)** を検索します。

1. このトリガー内で、 **[デバイス名]** という名前のテキスト入力を追加します。 説明のテキストを **[Enter the device name of your new device]\(新規デバイスのデバイス名を入力してください\)** に変更します。

1. 新しいアクションを追加します。 **[Azure IoT Central - Create a device]\(Azure IoT Central - デバイスの作成\)** アクションを検索します。

1. アプリケーションを選択し、デバイス作成元のデバイス テンプレートをドロップダウンから選択します。 [expand to show all the properties and settings of the device]\(デバイスのすべてのプロパティと設定を展開表示する\) というアクションが表示されます。

1. [デバイス名] フィールドを選択します。 [動的なコンテンツ] ウィンドウで、 **[デバイス名]** を選択します。 この値は、ユーザーがモバイル アプリで入力した入力値から渡され、IoT Central 内の新しいデバイスの名前になります。 この例では、必須フィールドは [デバイス名] のみで、赤のアスタリスクで示されています。 別のデバイス テンプレートでは、複数の必須フィールドを持つものもあり、これらのフィールドをすべて入力しないと、新しいデバイスを作成できません。

    ![Flow の [create device action]\(デバイス アクションの作成\) 動的ウィンドウ](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (省略可能) 新しいデバイスの作成に適していると思われるその他のフィールドに入力します。

1. 最後に、ワークフローを保存します。

1. Microsoft Flow モバイル アプリでワークフローを試してみます。 アプリの **[ボタン]** タブに移動します。 [Button -> Create a new device]\(ボタン -> デバイスの新規作成\) ワークフローが表示されます。 新しいデバイスの名前を入力し、その名前が IoT Central に表示されることを確認します。

    ![Flow の [デバイスの作成] モバイル スクリーンショット](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>ワークフロー内のデバイスを更新する

このセクションでは、Microsoft Flow モバイル アプリを使い、モバイル デバイスのボタンを押して IoT Central 内のデバイスの設定とプロパティを更新する方法について説明します。

1. 最初に Microsoft Flow 内で空のワークフローを作成します。

1. トリガーとして **[Flow button for mobile]\(モバイル用の Flow ボタン\)** を検索します。

1. このトリガー内で、変更する設定またはプロパティに対応するテキスト入力「**場所**」のような入力を追加します。 説明のテキストを変更します。

1. 新しいアクションを追加します。 **[Azure IoT Central - Update a device]\(Azure IoT Central - デバイスの更新\)** アクションを検索します。

1. ドロップダウンからアプリケーションを選択します。 ここで、更新する既存のデバイスの ID が必要になります。 

    > [!NOTE] 
    > 更新するデバイスのデバイス詳細ページにある **URL から見つかる ID を使用する必要があります**。 デバイス エクスプローラーのデバイス一覧から見つかるデバイス ID は、Microsoft Flow で使用する正しい ID ではありません。

    ![URL からの IoT Central ID](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. デバイス名を更新できます。 デバイスのプロパティと設定を更新するには、更新するデバイスのデバイス テンプレートを **[Device Template]\(デバイス テンプレート\)** ドロップダウンで選択する必要があります。 アクション タイルが展開し、更新可能なすべてのプロパティと設定が表示されます。

    ![Flow のデバイス更新ワークフロー](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. 更新するプロパティと設定をそれぞれ選択します。 [動的なコンテンツ] ウィンドウで、対応する入力をトリガーから選択します。 この例では、[場所] の値が伝達され、デバイスの [場所] プロパティが更新されます。

1. 最後に、ワークフローを保存します。

1. Microsoft Flow モバイル アプリでワークフローを試してみます。 アプリの **[ボタン]** タブに移動します。 [Button -> Update a device]\(ボタン -> デバイスの更新\) ワークフローが表示されます。 入力値を入力し、IoT Central でデバイスが更新されることを確認します。

## <a name="get-device-information-in-a-workflow"></a>ワークフロー内でデバイス情報を取得する

**[Azure IoT Central - Get a device]\(Azure IoT Central - デバイスの取得\)** アクションを使用し、その ID でデバイス情報を取得できます。 
> [!NOTE] 
> 更新するデバイスのデバイス詳細ページにある **URL から見つかる ID を使用する必要があります**。 デバイス エクスプローラーのデバイス一覧から見つかるデバイス ID は、Microsoft Flow で使用する正しい ID ではありません。

デバイス名、デバイス テンプレート名、プロパティ値、ワークフローの後続アクションに渡す設定値などの情報を取得できます。 デバイスから Microsoft Teams に顧客名のプロパティ値とともに渡されるワークフローの例を示します。

   ![Flow のデバイス取得ワークフロー](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>ワークフローのデバイスでコマンドを実行する
**Azure IoT Central - Run a command\(Azure IoT Central - コマンドの実行\)** アクションを使用し、その ID で指定されたデバイスでコマンドを実行することができます。 

> [!NOTE] 
> 更新するデバイスのデバイス詳細ページにある **URL から見つかる ID を使用する必要があります**。 デバイス エクスプローラーのデバイス一覧から見つかるデバイス ID は、Microsoft Flow で使用する正しい ID ではありません。
    
このアクションを通して、実行するコマンドを選択し、コマンドのパラメーターで渡すことができます。 Microsoft Flow モバイル アプリ内のボタンからデバイスの再起動コマンドを実行するワークフローの例を次に示します。

   ![Flow のデバイス取得ワークフロー](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>ワークフロー内のデバイスを削除する

**[Azure IoT Central - Delete a device]\(Azure IoT Central - デバイスの削除\)** アクションを使用して、その ID でデバイスを削除できます。 
> [!NOTE] 
> 更新するデバイスのデバイス詳細ページにある **URL から見つかる ID を使用する必要があります**。 デバイス エクスプローラーのデバイス一覧から見つかるデバイス ID は、Microsoft Flow で使用する正しい ID ではありません。

Microsoft Flow モバイル アプリ内のボタンを押してデバイスを削除するワークフローの例を次に示します。

   ![Flow の[デバイスの削除] ワークフロー](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>トラブルシューティング

Azure IoT Central コネクタへの接続を作成できない場合に役立つヒントを次に示します。

1. Microsoft 個人アカウント (@hotmail.com、@live.com、@outlook.com ドメインなど) は、現時点ではサポートされていません。 Azure Active Directory (AD) の職場または学校アカウントを使用する必要があります。

2. Microsoft Flow で IoT Central コネクタを使用するには、IoT Central アプリケーションに少なくとも 1 回サインインしたことがある必要があります。 1 回もサインインしたことがない場合、このアプリケーションは [アプリケーション] ドロップダウンに表示されません。

3. Azure AD アカウントを使用しているときにエラーが発生した場合は、Windows PowerShell を開き、次のコマンドを管理者として実行してみてください。

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>次の手順

ここでは、Microsoft Flow を使用して、ワークフローを作成する方法を学びました。推奨される次のステップは、[デバイスの管理](howto-manage-devices.md)です。

