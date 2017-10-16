---
title: "Azure Security Center におけるアダプティブ アプリケーション制御 | Microsoft Docs"
description: "このドキュメントは、Azure VM で実行されるアプリケーションのホワイトリストを作成するために、Azure Security Center でアダプティブ アプリケーション制御を使用する際に役に立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 9c3a9a7255bbbdab8f4c356eb07022d7f1d242d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Azure Security Center におけるアダプティブ アプリケーション制御 (プレビュー)
このチュートリアルを使用して、Azure Security Center でアプリケーション制御を構成する方法を説明します。

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Security Center でのアダプティブ アプリケーション制御とは
アダプティブ アプリケーション制御は、Azure 内の VM 上でどのアプリケーションを実行できるかを制御するために役立ちます。これにはさまざまな利点がありますが、たとえばマルウェアに対する VM の保護の強化に役立ちます。 Security Center は、機械学習によって VM で実行されているプロセスを分析し、この情報を利用することで、お客様がホワイトリスト登録に関する規則を適用するのを支援します。 この機能によって、アプリケーションのホワイトリストを構成および保守するプロセスが大幅に簡略化され、以下のことができるようになります。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の試行をブロックまたは警告する
- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。
- 環境内で使用されることが望ましくないソフトウェアを避ける。
- 古くてサポートされていないアプリが実行されることを避ける。
- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。
- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

> [!NOTE]
> アダプティブ アプリケーション制御は、制限付きのパブリック プレビューとして、Azure Security Center Standard のお客様が使用できます。 プレビューに参加するには、サブスクリプション ID を記入した電子メールを[こちら](mailto:ASC_appcontrol@microsoft.com)に送信してください。

## <a name="how-to-enable-adaptive-application-controls"></a>アダプティブ アプリケーション制御を有効にする方法
アダプティブ アプリケーション制御では、構成済みリソース グループに対する実行が許可される一連のアプリケーションを定義できます。 この機能が使用できるのは、Windows マシンのみです (すべてのバージョン、クラシックまたは Azure Resource Manager)。 Security Center でアプリケーションのホワイトリストを構成するには、以下の手順に従います。

1.  **[Security Center]** ダッシュボードを開き、**[概要]** をクリックします。
2.  **[高度なクラウド防御]** の **[Adaptive application controls]\(アダプティブ アプリケーション制御\)** タイルに、すべての VM のうち、いくつの VM が現在制御されているかが表示されます。 また、過去 1 週間で検出された問題の数も表示されます。 

    ![アダプティブ アプリケーション制御](./media/security-center-adaptive-application\security-center-adaptive-application-fig1.png)

3. **[Adaptive application controls]\(アダプティブ アプリケーション制御\)** タイルをクリックすると、他のオプションも表示されます。

    ![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

4. [リソース グループ] セクションには、3 つのタブがあります。
    * **[推奨]**: アプリケーション制御が推奨されるリソース グループの一覧。 Security Center では機械学習が使用され、VM が常に同じアプリケーションを実行しているかどうかに基づいて、アプリケーション制御の候補となる VM が特定されます。
    * **[構成済み]**: アプリケーション制御で構成された VM が含まれているリソース グループの一覧。 
    * **[推奨なし]**: アプリケーション制御の推奨がない VM が含まれているリソース グループの一覧。 たとえば、実行されるアプリケーションが常に変化していて、一定の状態にならない VM などです。

以降のセクションでは、各オプションの詳細と、その使用方法について説明します。

### <a name="configure-a-new-application-control-policy"></a>新しいアプリケーション制御ポリシーの構成
アプリケーション制御の推奨があるリソース グループの一覧を表示するには、**[推奨]** タブをクリックします。

![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

一覧には次の項目が含まれています。
- **[名前]**: サブスクリプションとリソース グループの名前
- **[VM]**: リソース グループ内の仮想マシンの数
- **[状態]**: 推奨の状態。ほとんどの場合はオープン
- **[重大度]**: 推奨の重大度レベル

**[Create application control rules]\(アプリケーション制御規則の作成\)** オプションを開くには、リソース グループを選択します。

![アプリケーション制御規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

**[VM の選択]** で、推奨されている VM の一覧を確認し、アプリケーション制御を適用しないすべての項目をオフにします。 **[ホワイトリスト登録に関する規則のプロセスを選択]** で、推奨されているアプリケーションの一覧を確認し、適用しないすべての項目をオフにします。 一覧には以下が含まれています。

- **[名前]**: アプリケーションの完全パス
- **[プロセス]**: 各パス内に存在するアプリケーションの数
- **[共通]**: true は、これらのプロセスがこのリソース グループ内のほとんどの VM で実行されていることを示します。
- **[利用可能]**: 警告アイコンは、アプリケーションがアプリケーション ホワイトリストをバイパスするために攻撃者によって使用されうることを示します。 これらのアプリケーションは、承認前に確認することを強くお勧めします。 

選択を終了したら、**[作成]** ボタンをクリックします。 既定では、Security Center は常にアプリケーション制御を "*監査*" モードで有効にします。 ホワイトリストによるワークロードへの悪影響がないことを検証した後で、"*強制*" モードに変更できます。

> [!NOTE]
> セキュリティのベスト プラクティスとして、Security Center ではホワイトリストに登録する必要があるアプリケーションの発行元規則の作成が常に試行されます。アプリケーションに発行元情報がない (つまり署名されていない) 場合にのみ、特定の EXE の完全パスに対するパス規則が作成されます。
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>アプリケーション制御で構成されたグループの編集および監視

アプリケーション制御で構成されたグループを編集および監視するには、**[リソース グループ]** の **[構成済み]** をクリックします。

![リソース グループ](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

一覧には次の項目が含まれています。

- **[名前]**: サブスクリプションとリソース グループの名前
- **[VM]**: リソース グループ内の仮想マシンの数
- **[モード]**: 監査モードでは、ホワイトリストに登録されていないアプリケーションの実行が試行されると、ログに記録されます。ブロックでは、ホワイトリストに登録されていないアプリケーションの実行が許可されません
- **[重大度]**: 推奨の重大度レベル

**[Edit application control policy]\(アプリケーション制御ポリシーの編集\)** ページで変更を行うには、リソース グループを選択します。

![保護](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

**[保護モード]** では、次のいずれかのオプションを選択できます。
- **[監査]**: このモードでは、アプリケーション制御ソリューションによって規則が強制されず、保護されている VM のアクティビティの監査だけが行われます。 このオプションをお勧めするのは、ターゲット VM でアプリの実行をブロックする前に、まず全体的な動作を観察したい場合です。
- **[強制]**: このモードでは、アプリケーション制御ソリューションによって規則が強制され、実行を許可されていないアプリケーションがブロックされるようになります。 

既に説明したように、既定では新しいアプリケーション制御ポリシーは常に "*監査*" モードで構成されます。 **[Policy extension]\(ポリシー拡張\)** で、ホワイトリストに登録する独自のアプリケーション パスを追加することができます。 これらのパスを追加すると、既に設定されている規則に加えて、これらのアプリケーション用の適切な規則が Security Center によって作成されます。 **[問題]** セクションには、現在の違反の一覧が表示されます。

![発行](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

たとえば、次のような種類です。

- **[問題]**: ログに記録されたすべての違反。次のような違反があります。
    - **[ViolationsBlocked]**: ソリューションで強制モードが有効になっていて、ホワイトリストに登録されていないアプリケーションの実行が試行された場合。
    - **[ViolationsAudited]**: ソリューションで監査モードが有効になっていて、ホワイトリストに登録されていないアプリケーションが実行された場合。
    - **[RulesViolatedManually]**: ユーザーが ASC 管理ポータルを通じてではなく手動で VM に対する規則を構成しようとした場合。
- **[VM の数]**: この種類の問題がある仮想マシンの数。

これらの行のいずれかをクリックすると、[Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ページにリダイレクトされます。このページでは、この種類の違反があるすべての VM について情報を確認できます。 各行の末尾にある 3 つの点をクリックすると、その特定の項目を削除できます。 **[Configured virtual machines]\(構成済み仮想マシン\)** セクションには、これらの規則が適用される VM の一覧が表示されます。 

![構成済み仮想マシン](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

**[発行元のホワイトリスト登録に関する規則]** には、各アプリケーションで検出された証明書情報に基づいて作成された発行元規則の対象となるアプリケーションの一覧が表示されます。 詳細については、[AppLocker の発行元規則の概要](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker)に関するページを参照してください。

![ホワイトリスト登録に関する規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

各行の末尾にある 3 つの点をクリックすると、その特定の規則を削除できます。 **[パスのホワイトリスト登録に関する規則]** には、デジタル証明書で署名されていないものの、まだホワイトリスト登録に関する規則で許可されている完全なアプリケーション パス (実行可能ファイルを含む) の一覧が表示されます。 

> [!NOTE]
> セキュリティのベスト プラクティスとして、Security Center ではホワイトリストに登録する必要がある EXE の発行元規則の作成が既定で常に試行されます。EXE に発行元情報がない (つまり署名されていない) 場合にのみ、特定の EXE の完全パスに対するパス規則が作成されます。

![パスのホワイトリスト登録に関する規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

一覧には、以下が含まれています。
- **[名前]**: 実行可能ファイルの完全パス
- **[利用可能]**: true は、アプリケーションがアプリケーション ホワイトリストをバイパスするために攻撃者によって使用されうることを示します。  

各行の末尾にある 3 つの点をクリックすると、その特定の規則を削除できます。 変更後、**[保存]** ボタンをクリックします。変更を適用しない場合は、**[破棄]** をクリックします。

### <a name="not-recommended-list"></a>非推奨の一覧

Security Center では、安定した一連のアプリケーションが実行されている仮想マシンについてのみ、アプリケーション ホワイトリストに登録することを推奨しています。 関連する VM 上のアプリケーションが常に異なる場合、推奨は作成されません。 

![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

一覧には、以下が含まれています。
- **[名前]**: サブスクリプションとリソース グループの名前。
- **[VM]**: リソース グループ内の仮想マシンの数。

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure VM で実行されるアプリケーションのホワイトリストを作成するために、Azure Security Center でアダプティブ アプリケーション制御を使用する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。 
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

