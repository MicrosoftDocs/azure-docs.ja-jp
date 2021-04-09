---
title: 新しいセッション ホストを使用して既存の Windows Virtual Desktop (クラシック) ホスト プールを拡張する - Azure
description: Windows Virtual Desktop (クラシック) で新しいセッション ホストを使用して既存のホスト プールを拡張する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009173"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) で新しいセッション ホストを使用して既存のホスト プールを拡張する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../expand-existing-host-pool.md)を参照してください。

ホスト プール内の使用率を上げた場合、新しいセッション ホストで既存のホスト プールを拡張して、新しい負荷を処理する必要がある場合があります。

この記事では、新しいセッション ホストで既存のホスト プールを拡張する方法について説明します。

## <a name="what-you-need-to-expand-the-host-pool"></a>ホスト プールを拡張するために必要なもの

開始する前に、次のいずれかの方法を使用してホスト プールとセッション ホスト仮想マシン (VM) を作成済みであることを確認します。

- [Azure Marketplace オファリング](create-host-pools-azure-marketplace-2019.md)
- [GitHub の Azure Resource Manager テンプレート](create-host-pools-arm-template.md)
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell-2019.md)

ホスト プールとセッション ホスト VM を最初に作成したときから、次の情報も必要になります。

- VM のサイズ、イメージ、および名前のプレフィックス
- ドメイン参加と Windows Virtual Desktop テナント管理者の資格情報
- 仮想ネットワーク名とサブネット名

以下の 3 つのセクションは、ホスト プールを拡張するために使用できる 3 つの方法です。 いずれでも使いやすいデプロイ ツールを使用できます。

>[!NOTE]
>デプロイ フェーズ中では、以前のセッション ホスト VM リソースが現在シャットダウンされている場合、それらのエラー メッセージが表示されます。 これらのエラーは、Azure が PowerShell DSC 拡張機能を実行してセッション ホスト VM が既存のホスト プールに正しく登録されていることを検証できないために発生します。 これらのエラーは無視しても問題ありません。または、デプロイ プロセスを開始する前に、既存のホスト プール内のすべてのセッション ホスト VM を開始することで、エラーを回避できます。

## <a name="redeploy-from-azure"></a>Azure からの再デプロイ

[Azure Marketplace オファリング](create-host-pools-azure-marketplace-2019.md)または [GitHub の Azure Resource Manager テンプレート](create-host-pools-arm-template.md)を使用してホスト プールおよびセッション ホスト VM を既に作成している場合は、Azure portal から同じテンプレートを再デプロイできます。 テンプレートを再デプロイすると、パスワードを除いて、元のテンプレートに入力したすべての情報が自動的に再入力されます。

Azure Resource Manager テンプレートを再デプロイしてホスト プールを拡張する方法を次に示します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の上部にある検索バーで「**リソース グループ**」を検索し、 **[サービス]** の項目を選択します。
3. ホスト プールを作成したときに作成したリソース グループを見つけて選択します。
4. ブラウザーの左側にあるパネルで、 **[デプロイ]** を選択します。
5. ホスト プールの作成プロセスに適したデプロイを選択します。
     - Azure Marketplace オファリングを使用して元のホスト プールを作成した場合は、**rds.wvd-provision-host-pool** で始まるデプロイを選択します。
     - GitHub の Azure Resource Manager テンプレートを使用して元のホスト プールを作成した場合は、**Microsoft.Template** という名前のデプロイを選択します。
6. **[再デプロイ]** を選択します。

     >[!NOTE]
     >**[再デプロイ]** を選択してもテンプレートが自動的に再デプロイされない場合は、ブラウザーの左側のパネルで **[テンプレート]** を選択し、 **[デプロイ]** を選択します。

7. 既存のホスト プール内の現在のセッション ホスト VM を含むリソース グループを選択します。

     >[!NOTE]
     >入力したリソース グループが正しいにもかかわらず別のリソース グループを選択するように求めるエラーが表示される場合は、別のリソース グループを選択し、元のリソース グループを選択します。

8. *_artifactsLocation* に `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/` の URL を入力します。
9. 必要なセッション ホストの新しい合計数を *[Rdsh Number Of Instances]\(Rdsh のインスタンス数\)* に入力します。 たとえば、ホスト プールを 5 つのセッション ホストから 8 つに拡張する場合は、「**8**」と入力します。
10. 既存のドメイン UPN に使用したものと同じ既存のドメイン パスワードを入力します。 ユーザー名は変更しないでください。テンプレートを実行するとエラーが発生します。
11. *[Tenant Admin Upn Or Application Id]\(テナント管理者 UPN またはアプリケーション ID\)* に入力したユーザーまたはアプリケーション ID に使用したものと同じテナント管理者パスワードを入力します。ここでも、ユーザー名は変更しないでください。
12. 送信を完了すると、ホスト プールは拡張されます。

## <a name="run-the-azure-marketplace-offering"></a>Azure Marketplace オファリングを実行する

「[Azure Marketplace を使用してホスト プールを作成する](create-host-pools-azure-marketplace-2019.md)」の手順を「[新しいホスト プールをプロビジョニングするための Azure Marketplace オファリングを実行する](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)」まで実行します。 このポイントに到達したら、各タブについて次の情報を入力する必要があります。

### <a name="basics"></a>基本

このセクションのすべての値は、ホスト プールとセッション ホスト VM を最初に作成したときに指定したものと一致する必要があります。ただし、 *[Default desktop users]\(既定のデスクトップ ユーザー\)* を除きます。

1.    *[サブスクリプション]* の場合、ホスト プールを最初に作成したサブスクリプションを選択します。
2.    *[リソース グループ]* には、既存のホスト プール セッション ホスト VM が配置されている同じリソース グループを選択します。
3.    *[リージョン]* には、既存のホスト プール セッション ホスト VM が配置されている同じリージョンを選択します。
4.    *[Hostpool name]\(ホストプール名\)* には、既存のホスト プールの名前を入力します。
5.    *[Desktop type]\(デスクトップの種類\)* では、既存のホスト プールと同じデスクトップの種類を選択します。
6.    *[Default desktop users]\(既定のデスクトップ ユーザー\)* には、Windows Virtual Desktop クライアントにサインインし、Azure Marketplace のオファリングが終了した後にデスクトップにアクセスできるようにする追加ユーザーのコンマ区切り一覧を入力します。 たとえば、user3@contoso.com と user4@contoso.com に対してアクセス権を割り当てる場合、「user3@contoso.com,user4@contoso.com」と入力します。
7.    **[次へ :Configure virtual machine]\(仮想マシンの構成\)** を選択します。

>[!NOTE]
>*[Default desktop users]\(既定のデスクトップ ユーザー\)* を除き、すべてのフィールドは、既存のホスト プールで構成されているものと正確に一致する必要があります。 不一致がある場合は、新しいホスト プールが生成されます。

### <a name="configure-virtual-machines"></a>仮想マシンの構成

このセクションのすべてのパラメーター値は、VM の合計数を除き、ホスト プールおよびセッション ホスト VM を最初に作成したときに指定したものと一致する必要があります。 入力する VM 数は、拡張ホスト プール内の VM の数になります。

1. 既存のセッション ホスト VM と一致する VM サイズを選択します。

    >[!NOTE]
    >お探しの VM サイズが VM サイズ セレクターに表示されない場合、まだ Azure Marketplace ツールにオンボードされていないのが原因です。 特定の VM サイズを要求するには、[Windows Virtual Desktop UserVoice フォーラム](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)で要求を作成するか、既存の要求に賛成票を投じてください。

2. *[使用プロファイル]* 、 *[ユーザー総数]* 、および *[Number of virtual machines]\(仮想マシン数\)* パラメーターをカスタマイズして、ホスト プールに含めるセッション ホストの合計数を選択します。 たとえば、ホスト プールを 5 つのセッション ホストから 8 つに拡張する場合、8 つの仮想マシンになるようにこれらのオプションを構成します。
3. 仮想マシンの名前のプレフィックスを入力します。 たとえば、"prefix" という名前を入力する場合であれば、仮想マシンの名前は "prefix-0"、"prefix-1" などのようになります。
4. **[次へ :Virtual machine settings]\(仮想マシンの設定\)** を選択します。

### <a name="virtual-machine-settings"></a>仮想マシンの設定

このセクションのすべてのパラメーター値は、ホスト プールとセッション ホスト VM を最初に作成したときに指定したものと一致する必要があります。

1. *[イメージ ソース]* と *[Image OS version]\(イメージの OS バージョン\)* には、ホスト プールを最初に作成したときに指定したものと同じ情報を入力します。
2. *[AD domain join UPN]\(AD ドメイン参加 UPN\)* と関連付けられているパスワードには、VM を Active Directory ドメインに参加させるために最初にホスト プールを作成したときに指定したものと同じ情報を入力します。 これらの資格情報は、仮想マシンでローカル アカウントを作成するために使用されます。 後でこれらのローカル アカウントをリセットして資格情報を変更できます。
3. 仮想ネットワーク情報については、既存のホスト プール セッション ホスト VM が配置されている同じ仮想ネットワークとサブネットを選択します。
4. **[次へ :Windows Virtual Desktop information]\(Windows Virtual Desktop 情報\)** を選択します。

### <a name="windows-virtual-desktop-information"></a>Windows Virtual Desktop の情報

このセクションのすべてのパラメーター値は、ホスト プールとセッション ホスト VM を最初に作成したときに指定したものと一致する必要があります。

1. *[Windows Virtual Desktop tenant group name]\(Windows Virtual Desktop テナント グループ名\)* に、テナントが含まれるテナント グループの名前を入力します。 具体的なテナント グループ名を指定されていないかぎり、既定値のままにしてください。
2. *[Windows Virtual Desktop tenant name]\(Windows Virtual Desktop テナント名\)* に、このホスト プールの作成先となるテナントの名前を入力します。
3. ホスト プールとセッション ホスト VM を最初に作成したときに使用したものと同じ資格情報を指定します。 サービス プリンシパルを使用している場合、サービス プリンシパルが配置されている Azure Active Directory インスタンスの ID を入力します。
4. **[次へ :確認と作成]** をクリックします。

## <a name="run-the-github-azure-resource-manager-template"></a>GitHub の Azure Resource Manager テンプレートを実行する

「[新規ホスト プールのプロビジョニングのための Azure Resource Manager テンプレートの実行](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)」の手順を実行し、 *[Rdsh Number Of Instances]\(Rdsh のインスタンス数\)* を除き、すべて同じパラメーターを指定します。 テンプレートを実行した後に、ホスト プールに必要なセッション ホスト VM の数を入力します。 たとえば、ホスト プールを 5 つのセッション ホストから 8 つに拡張する場合は、「**8**」と入力します。

## <a name="next-steps"></a>次のステップ

既存のホスト プールを拡張したので、Windows Virtual Desktop クライアントにサインインして、ユーザー セッションの一部としてそれらをテストできるようになりました。 次のいずれかのクライアントとのセッションに接続できます。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-10-2019.md)
- [Web クライアントに接続する](connect-web-2019.md)
- [Android クライアントに接続する](connect-android-2019.md)
- [macOS クライアントに接続する](connect-macos-2019.md)
- [iOS クライアントに接続する](connect-ios-2019.md)
