---
title: Azure-SSIS 統合ランタイムのライセンス付きコンポーネントのインストール
description: ISV が Azure SSIS 統合ランタイムの有料 (ライセンスあり) コンポーネントをどのようにして開発し、インストールできるかを学習します
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386290"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムの有料 (ライセンスあり) カスタム コンポーネントをインストールする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure SSIS 統合ランタイムにおいて、Azure で実行される SQL Server Integration Services (SSIS) パッケージのための有料 (ライセンスあり) コンポーネントを ISV がどのようにして開発し、インストールできるかについて説明します。

## <a name="the-problem"></a>問題

Azure SSIS 統合ランタイムは、その性質上、いくつかの難題を提示します。そのため、カスタム コンポーネントのオンプレミス インストールに使用される一般的なライセンス供与方法では不十分となります。 その結果、Azure SSIS IR で別の手法が必要となります。

-   Azure SSIS IR のノードは一時的なものであり、いつでも割り当てたり、リリースしたりできます。 たとえば、ノードを開始したり、停止したりすることでコストを管理し、ノード サイズをスケールアップまたはスケールダウンします。 結果的に、MAC アドレスや CPU ID など、コンピューター固有の情報をサードパーティ コンポーネント ライセンスを特定のノードにバインドできなくなります。

-   また、ノードの数をいつでも拡大または縮小できるように、Azure SSIS IR をスケールインまたはスケールアウトできます。

## <a name="the-solution"></a>解決策

前のセクションで説明されている従来のライセンス方法の制限の結果、Azure SSIS IR は新しい解決策を提供します。 この解決策では、Windows 環境変数と SSIS システム変数を使用して、サード パーティ コンポーネントのライセンスのバインドと検証を行います。 ISV はこのような変数を使用し、クラスター ID やクラスター ノード数など、Azure SSIS IR の固有かつ永続的な情報を取得できます。 この情報を使用することで、ISV は *クラスターとしての* Azure SSIS IR にコンポーネントのライセンスをバインドできます。 このバインドでは、顧客が任意の方法で Azure SSIS IR を開始または停止、スケールアップまたはスケールダウン、スケールインまたはスケールアウト、もしくは再構成したときに変更されない ID を使用します。

次の図は、これらの新しい変数を使用するサードパーティ コンポーネントの一般的なインストール、ライセンス認証、ライセンス バインディング、検証のフローを示したものです。

![ライセンス コンポーネントのインストール](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>手順
1. ISV はさまざまな SKU または階層でライセンス コンポーネントを提供できます (シングル ノード、最大 5 個のノード、最大 10 個のノードなど)。 ISV は、顧客が製品を購入したときにプロダクト キーを提供します。 ISV は、ISV セットアップ スクリプトと関連ファイルが含まれる Azure Storage BLOB コンテナーも提供します。 顧客はこれらのファイルを自分のストレージ コンテナーに複製し、自分のプロダクト キーで変更できます (たとえば、`IsvSetup.exe -pid xxxx-xxxx-xxxx` を実行します)。 顧客は次に、自分のコンテナーの SAS URI をパラメーターとして Azure SSIS IR をプロビジョニングしたり、再構成したりできます。 詳細については、「[Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)」(Azure-SSIS 統合ランタイムのカスタム設定) を参照してください。

2. Azure SSIS IR がプロビジョニングまたは再構成されると、ISV セットアップが各ノードで実行され、Windows 環境変数の `SSIS_CLUSTERID` と `SSIS_CLUSTERNODECOUNT` にクエリを実行します。 次に、Azure SSIS IR はそのクラスター ID とライセンス製品のプロダクト キーを ISV ライセンス認証サーバーに送信し、ライセンス認証キーを生成します。

3. ライセンス認証キーを受け取ると、ISV セットアップでは、各ノードにキーをローカル保存できます (たとえば、レジストリに)。

4. Azure SSIS IR のノードで ISV のライセンス コンポーネントを使用するパッケージを顧客が実行すると、そのパッケージはローカル保存されているライセンス認証キーを読み込み、ノードのクラスター ID に基づいて有効性を確認します。 そのパッケージはまた、クラスター ノード数を ISV ライセンス認証サーバーに任意で報告できます。

    次は、ライセンス認証キーの有効性を確認し、クラスター ノード数を報告するコード例です。

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV パートナー

[ADF での SSIS の Enterprise Edition、カスタム セットアップ、およびサード パーティ機能拡張](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360)に関するブログ記事の最後で、コンポーネントおよび拡張機能を Azure SSIS IR に適応させている ISV パートナーの一覧を検索できます。

## <a name="next-steps"></a>次のステップ

-   [Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure SSIS 統合ランタイムの Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
