---
title: 起動しないロールのトラブルシューティング | Microsoft Docs
description: クラウド サービス ロールが起動に失敗する一般的な原因をいくつか取り上げます。 これらの問題に対する解決策も紹介します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743221"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>起動できない Azure クラウド サービス (クラシック) ロールをトラブルシューティングする

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

ここでは、Azure Cloud Services ロールの起動失敗に関連した一般的な問題と解決法を取り上げます。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL または依存コンポーネントの欠落
ロールが応答しない、 **[初期化しています]** 、 **[ビジー]** 、 **[停止しています]** の状態を繰り返す、といった症状は、DLL やアセンブリの不足が原因で起こる場合があります。

DLL やアセンブリの不足は、次のような症状を引き起こします。

* ロール インスタンスが **[初期化しています]** 、 **[ビジー]** 、 **[停止しています]** の状態を繰り返す。
* ロール インスタンスが **[準備完了]** 状態に移行したにもかかわらず、Web アプリケーションにアクセスしてもページが表示されない。

これらの問題に関して推奨される調査方法がいくつかあります。

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>DLL の欠落を Web ロールで診断する
Web ロールにデプロイされている Web サイトにアクセスし、ブラウザーに次のようなサーバー エラーが表示されるとき、それは DLL の欠落を意味している可能性があります。

![Server Error in '/' Application.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>カスタム エラーをオフにして問題を診断する
Web ロールの web.config でカスタム エラー モードをオフに設定して再度サービスをデプロイすると、もっと詳しいエラー情報を表示できます。

リモート デスクトップを使用せずに詳細なエラーを表示するには

1. Microsoft Visual Studio でソリューションを開きます。
2. **ソリューション エクスプローラー** で、web.config ファイルを探して開きます。
3. web.config ファイルの system.web セクションを探し、次の行を追加します。

    ```xml
    <customErrors mode="Off" />
    ```
4. ファイルを保存します。
5. サービスをパッケージし直して再度デプロイします。

サービスを再度デプロイすると、不足しているアセンブリまたは DLL の名前と共にエラー メッセージが表示されます。

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>遠隔からエラーを参照して問題を診断する
離れた場所からリモート デスクトップを使用してロールにアクセスし、詳細なエラー情報を参照することができます。 リモート デスクトップを使用してエラーを参照するには、次の手順に従います。

1. Azure SDK 1.3 以上がインストールされていることを確認します。
2. Visual Studio を使ったソリューションのデプロイの間に、リモート デスクトップを有効にします。 詳しくは、「[Enable Remote Desktop Connection for a Role in Azure Cloud Services using Visual Studio (Visual Studio を使用して Azure Cloud Services のロールでリモート デスクトップ接続を有効にする)](cloud-services-role-enable-remote-desktop-visual-studio.md)」をご覧ください。
3. Microsoft Azure Portal で、インスタンスのステータスが **[準備完了]** と表示されたら、インスタンスにリモート接続します。 Cloud Services でリモート デスクトップを使う方法について詳しくは、「[ロール インスタンスへのリモート接続](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)」をご覧ください。
5. リモート デスクトップの構成中に指定した資格情報を使用して仮想マシンにサインインします。
6. コマンド ウィンドウを開きます。
7. 「`IPconfig`.
8. IPV4 アドレスの値をメモします。
9. Internet Explorer を開きます。
10. Web アプリケーションのアドレスと名前を入力します。 たとえば、「 `http://<IPV4 Address>/default.aspx` 」のように入力します。

Web サイトにアクセスすると、詳しいエラー メッセージが表示されます。

* Server Error in '/' Application.
* 説明: 現在の Web 要求を実行中に、ハンドルされていない例外が発生しました。 エラーに関する詳細および例外の発生場所については、スタック トレースを参照してください。
* 例外の詳細: System.IO.FIleNotFoundException: ファイルまたはアセンブリ ’Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’、またはその依存関係の 1 つが読み込めませんでした。 指定されたファイルが見つかりません。

次に例を示します。

![Explicit Server Error in '/' Application](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Compute Emulator を使用した問題の診断
依存コンポーネントの不足や web.config エラーに関する問題の診断とトラブルシューティングは、Microsoft Azure Compute Emulator を使用して行うことができます。

この診断方法の効果を最大限に高めるには、Windows がクリーン インストールされたコンピューターまたは仮想マシンを使用する必要があります。 Azure 環境のシミュレーションには、Windows Server 2008 R2 x64 が最適です。

1. スタンドアロン バージョンの [Azure SDK](https://azure.microsoft.com/downloads/)をインストールします。
2. 開発コンピューターで、クラウド サービスのプロジェクトをビルドします。
3. エクスプローラーで、クラウド サービス プロジェクトの bin\debug フォルダーに移動します。
4. デバッグ用のコンピューターに .csx フォルダーと .cscfg ファイルをコピーします。
5. クリーン インストールされたコンピューターで Azure SDK コマンド プロンプト ウィンドウを開き、「 `csrun.exe /devstore:start`」と入力します。
6. コマンド プロンプトで、「`run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`」と入力します。
7. ロールが起動すると、詳しいエラー情報が Internet Explorer に表示されます。 Windows の標準的なトラブルシューティング ツールを使用して、さらに詳しく問題を診断することもできます。

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace を使用して問題を診断する
.NET Framework 4 を使用する worker ロールと Web ロールに関しては、[IntelliTrace](/visualstudio/debugger/intellitrace) (Microsoft Visual Studio Enterprise で利用可能) を使用することができます。

IntelliTrace を有効にしてサービスをデプロイするには、以下の手順に従います。

1. Azure SDK 1.3 以上がインストールされていることを確認します。
2. Visual Studio を使用してソリューションをデプロイします。 デプロイメント中は、 **[.NET 4 のロールに対して IntelliTrace を有効にします]** チェック ボックスをオンにしてください。
3. インスタンスが起動したら、 **サーバー エクスプローラー** を開きます。
4. **Azure\\Cloud Services** ノードを展開し、対象のデプロイを特定します。
5. ロール インスタンスが表示されるまでデプロイメントを展開します。 いずれかのインスタンスを右クリックします。
6. **[IntelliTrace ログの表示]** を選択します。 **[IntelliTrace の概要]** が表示されます。
7. 概要の例外セクションを探します。 例外が存在する場合、そのセクションには **[例外データ]** という見出しが付きます。
8. **[例外データ]** を展開し、次のような **System.IO.FileNotFoundException** エラーを探します。

![Exception data, missing file or assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>不足している DLL とアセンブリの解決
不足している DLL とアセンブリのエラーを解決するには、次の手順を実行します。

1. Visual Studio でソリューションを開きます。
2. **ソリューション エクスプローラー** で **[参照]** フォルダーを開きます。
3. エラーに表示されているアセンブリをクリックします。
4. **[プロパティ]** ウィンドウで **[ローカル コピー]** プロパティを探し、その値を **[True]** に設定します。
5. クラウド サービスを再デプロイします。

すべてのエラーを修正済みであることが確認できたら、 **[.NET 4 のロールに対して IntelliTrace を有効にします]** チェック ボックスをオフにしてサービスをデプロイできます。

## <a name="next-steps"></a>次のステップ
クラウド サービスの他の [トラブルシューティングに関する記事](../index.yml?product=cloud-services&tag=top-support-issue) を参照します。

Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、 [Kevin Williamson によるブログ シリーズ](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)をご覧ください。
