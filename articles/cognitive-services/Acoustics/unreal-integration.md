---
title: Project Acoustics Unreal と Wwise の統合
titlesuffix: Azure Cognitive Services
description: この記事では、Project Acoustics Unreal および Wwise プラグインのプロジェクトへの統合について説明します。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243059"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal と Wwise の統合
この記事では、Project Acoustics プラグイン パッケージを既存の Unreal と Wwise のゲーム プロジェクトに統合する方法について説明します。

ソフトウェア要件:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 以降
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Unreal 用の Wwise プラグイン](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Wwise Unreal プラグインの代わりに Wwise SDK の直接統合を使用する場合は、Project Acoustics Unreal プラグインを調べて、Wwise API 呼び出しを調整します。

Wwise 以外のオーディオ エンジンと共に Project Acoustics を使用するには、[Project Acoustics ディスカッション フォーラム](https://github.com/microsoft/ProjectAcoustics/issues)で機能強化要求を行ってください。 Project Acoustics Unreal プラグインを使用して音響データのクエリを実行して、エンジンに対して API 呼び出しを行うことができます。

## <a name="download-project-acoustics"></a>Project Acoustics のダウンロード
[Project Acoustics Unreal と Wwise プラグイン パッケージ](https://www.microsoft.com/download/details.aspx?id=58090)をダウンロードします (まだ行っていない場合)。

Unreal Engine プラグインと Wwise ミキサー プラグインはパッケージに含まれています。 Unreal プラグインでは、エディターとランタイムの統合が提供されます。 ゲームプレイ中に、Project Acoustics Unreal プラグインでは、フレームごとの各ゲーム オブジェクトのオクルージョンなどのパラメーターが計算されます。 これらのパラメーターは、Wwise API 呼び出しに変換されます。

## <a name="integration-steps"></a>統合手順

パッケージをインストールしてゲームにデプロイするには、次の手順に従います。

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Project Acoustics ミキサー プラグインをインストールする
1. Wwise ランチャーを開きます。 **[Plugins]\(プラグイン\)** タブの **[Install New Plugins]\(新しいプラグインのインストール\)** で、 **[Add From Directory]\(ディレクトリから追加\)** を選択します。

    ![Wwise ランチャーにプラグインをインストールする](media/wwise-install-new-plugin.png)

1. ダウンロード パッケージに含まれている *AcousticsWwisePlugin\ProjectAcoustics* ディレクトリを選択します。 これには Wwise ミキサー プラグイン バンドルが含まれています。

   Wwise によってプラグインがインストールされます。 Wwise にインストールされているプラグインの一覧に Project Acoustics が表示されます。  
![Project Acoustics インストール後のインストールされた Wwise プラグインの一覧](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>ゲームに Wwise を再デプロイする
Wwise を既に統合している場合でも、ゲームに Wwise を再デプロイします。 この手順により、Project Acoustics Wwise プラグインが統合されます。

   > [!NOTE]
   > **エンジン プラグイン:** Wwise を Unreal C++ プロジェクトにゲーム プラグインとしてインストールした場合は、この手順はスキップしてください。 たとえば Unreal プロジェクトがブループリント専用であるため Wwise をエンジン プラグインとしてインストールした場合、ミキサー プラグインを含む Wwise のデプロイはより複雑になります。 空の Unreal C++ のダミー プロジェクトを作成します。 Unreal エディターが開いている場合は閉じて、残りの手順に従って Wwise をダミー プロジェクトにデプロイします。 次に、デプロイされた Wwise プラグインをコピーします。
 
1. Wwise ランチャーから、 **[Unreal Engine]** タブを選択します。 **[Recent Unreal Engine Projects]\(最近使った Unreal Engine プロジェクト\)** の横にある "ハンバーガー" (アイコン) メニューを選択して、 **[Browse for project]\(プロジェクトの参照\)** を選択します。 ゲームの Unreal プロジェクト *.project* ファイルを開きます。

    ![Wwise ランチャーの [Unreal] タブ](media/wwise-unreal-tab.png)

1. **[Integrate Wwise in Project]\(プロジェクトでの Wwise の統合\)** または **[Modify Wwise in Project]\(プロジェクトでの Wwise の変更\)** を選択します。 この手順では、Project Acoustics ミキサー プラグインが含まれているプロジェクトに Wwise バイナリを統合します。

   > [!NOTE]
   > **エンジン プラグイン:** Wwise をエンジン プラグインとして使用していて、前述のようにダミー プロジェクトを作成した場合は、Wwise をデプロイしたフォルダー *[DummyUProject]\Plugins\Wwise* をコピーします。 それを *[UESource]\Engine\Plugins\Wwise* に貼り付けます。 *[DummyUProject]* は空の Unreal C++ プロジェクト パスで、 *[UESource]* は Unreal Engine のソースがインストールされている場所です。 フォルダーをコピーしたら、ダミー プロジェクトは削除できます。

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>ゲームに Project Acoustics Unreal プラグインを追加する
 
1. プラグイン パッケージの *Unreal\ProjectAcoustics* フォルダーをコピーします。 新しいフォルダー *[UProjectDir]\Plugins\ProjectAcoustics* を作成します。ここで、 *[UProjectDir]* は、 *.uproject* ファイルを含むゲームのプロジェクト フォルダーです。

   > [!NOTE]
   > **エンジン プラグイン**:Wwise をエンジン プラグインとして使用している場合は、Project Acoustics を Unreal のエンジン プラグインとして使用する必要もあります。 前に言及したディレクトリではなく、 *[UESource]\Engine\Plugins\ProjectAcoustics* を使用します。

1. *ProjectAcoustics* フォルダーと共に *Wwise* フォルダーが表示されることを確認します。 これには、以前にデプロイしたミキサー プラグインのバイナリと共に Wwise プラグインが含まれています。

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal プラグイン機能を拡張する
Project Acoustics Unreal プラグインでは、[こちらのガイドライン](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)に従って Wwise Unreal プラグイン API から追加動作を公開する必要があります。 修正プログラムの適用の手順を自動化するバッチ ファイルを含めました。

* *Plugins\ProjectAcoustics\Resources* 内で、*PatchWwise.bat* を実行します。 次の画像の例では、この AcousticsGame サンプル プロジェクトを使用しています。

    ![Wwise にパッチを適用するスクリプトが強調表示されている Windows エクスプローラー ウィンドウ](media/patch-wwise-script.png)

* DirectX SDK がインストールされていない場合:ご使用の Wwise のバージョンによっては、*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* で `DXSDK_DIR` が含まれる行をコメント アウトする必要がある場合があります。

    ![コメント アウトされた `DXSDK` が示されているコード エディター](media/directx-sdk-comment.png)

* Visual Studio 2019 を使用してコンパイルする場合:Wwise を使用してリンク エラーを回避するには、*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* の既定の `VSVersion` 値を手動で **vc150** に変更します。

    !["VSVersion" が "vc150" に変更されたことを示しているコード エディター](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>ゲームをビルドし、Python が有効になっていることを確認する

1. ゲームをコンパイルし、正しくビルドされることを確認します。 ビルドされない場合は、先に進む前に、前の手順を慎重に確認してください。

1. Unreal エディターでプロジェクトを開きます。

    > [!NOTE]
    > **エンジン プラグイン:** ProjectAcoustics をエンジン プラグインとして使用している場合は、"組み込み" プラグインの下でそれが有効になっていることも確認します。

    Project Acoustics が統合されていることを示す新しいモードが表示されます。

    ![Unreal の Acoustics Mode Full](media/acoustics-mode-full.png)

1. エディターの統合が正しく機能するように、Unreal 用の Python プラグインが有効になっていることを確認します。

    ![Unreal エディターで有効になっている Python 拡張機能](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Project Acoustics を使用するように Wwise プロジェクトを設定する

Wwise プロジェクトの例は、サンプル ダウンロードに含まれています。 それを確認してこれらの手順に従うことをお勧めします。 この記事の後半にあるスクリーンショットは、このプロジェクトからのものです。

#### <a name="bus-setup"></a>バスのセットアップ
Project Acoustics Unreal プラグインでは、正確な名前 `Project Acoustics Bus` のバス上で、関連付けられているミキサー プラグインが検索されます。 これと同じ名前の新しいオーディオ バスを作成します。 ミキサー プラグインは、さまざまな構成で動作します。 しかしここでは、リバーブ処理にのみ使用されることを想定しています。 このバスでは、音響を使用するすべてのソースに対してミキシングされたリバーブ信号が搬送されます。 上流を任意のバスミックス構造に混在させることができます。 サンプル ダウンロードに含まれている Wwise のサンプル プロジェクトからの例をここに示します。

![Project Acoustics バスを示している Wwise バス](media/acoustics-bus.png)

1. バスのチャネル構成を *1.0*、*2.0*、*4.0*、*5.1*、*7.1* のいずれかに設定します。 その他の設定は、バスに出力されません。

    ![Project Acoustics バスのチャネル構成オプション](media/acoustics-bus-channel-config.png)

1. Project Acoustics バスの詳細に進み、 **[ミキサー プラグイン]** タブが表示されることを確認します。

    ![Wwise と Project Acoustics バスが有効になっている [ミキサー プラグイン] タブ](media/mixer-tab-enable.png)

1. **[ミキサー プラグイン]** タブに移動し、Project Acoustics ミキサー プラグインをバスに追加します。

    ![Wwise バスへの Project Acoustics ミキサー プラグインの追加方法を示している図](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>アクターミキサー階層のセットアップ
最適なパフォーマンスを得るために、Project Acoustics では、オーディオ デジタル信号処理がすべてのソースに同時に適用されます。 そのため、プラグインはミキサー プラグインとして動作する必要があります。 通常、出力バスはドライ出力信号を搬送しますが、Wwise ではミキサー プラグインが出力バス上に存在する必要があります。 Project Acoustics では、ドライ信号が Aux バスを介してルーティングされる一方、ウェット信号が `Project Acoustics Bus` 上で実行される必要があります。 次のプロセスでは、この信号フローへの段階的な移行がサポートされます。

*足音*、*武器*、その他を最上位レベルに含むアクター ミキサー階層を持つ既存のプロジェクトがあるとします。 それぞれに、そのドライ ミックスに対応する出力バスがあります。 音響を使用するために足音を移行するとしましょう。 まず、足音出力バスの子であるドライ サブミックスを搬送するために、対応する Aux バスを作成します。 たとえば、次の図では "Dry" プレフィックスを使用してバスを整理しましたが、正確な名前は重要ではありません。 足音バスに対する測定や効果は以前と同様に機能します。

![推奨される Wwise ドライ ミックスのセットアップ](media/wwise-dry-mix-setup.png)

次に、*Project Acoustics バス*を**出力バス**として設定し、*Dry_Footsteps* をユーザー定義の Aux バスとして設定して、足音のアクター ミキサーのバスの出力構造を次のように変更します。

![推奨される Wwise のアクター ミキサー バスのセットアップ](media/actor-mixer-bus-settings.png)

ここで、すべての足音が音響処理され、Project Acoustics Bus にリバーブを出力します。 ドライ信号は Dry_Footsteps 経由でルーティングされ、通常どおり立体化されます。

Project Acoustics は、世界に 3D の場所を持つサウンドにのみ適用されます。 [Wwise ドキュメント](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)に従って、示されているように配置プロパティを設定する必要があります。 **[3D Spatialization]\(3D 立体化\)** 設定は、必要に応じて *[Position]\(位置\)* または *[Position + Orientation]\(位置 + 方向\)* にすることができます。

![推奨される Wwise のアクター ポジション設定](media/wwise-positioning.png)

**出力バス**を、上流を *Project Acoustics* バスに混在させる他のバスに設定することはできません。 Wwise では、ミキサー プラグインにこの要件が課されます。

足音アクターミキサー階層内の子に音響を使用しない場合は、"親のオーバーライド" を使用してオプトアウトできます。

ゲームが定義したまたはユーザーが定義した送信をゲームの任意のアクターミキサー上のリバーブに使用している場合は、リバーブを 2 回適用しないようにアクターミキサー上でそれらをオフにします。

#### <a name="spatialization"></a>立体化
Project Acoustics Wwise ミキサー プラグインではコンボリューション リバーブが既定で適用され、Wwise にはパン立体化が残されます。 この既定のリバーブのみの構成で Project Acoustics を使用すると、ドライ ミックスで任意のチャネル構成と立体化方法を使用できます。 そのため、ほぼすべての立体化を Project Acoustics リバーブと組み合わせることができます。 オプションには、[Ambisonics ベースのバイノーラル立体化処理](https://www.audiokinetic.com/products/ambisonics-in-wwise/)と [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) が含まれます。
 
Project Acoustics には、オブジェクトベースの高解像度 HRTF レンダリングとパンの両方をサポートするオプションの立体化処理が含まれています。 ミキサー プラグインの設定の **[Perform Spatialization]\(立体化の実行\)** チェックボックスを選択して、 *[HRTF]* または *[Panning]\(パン\)* のいずれかを選択します。 また、Project Acoustics ミキサー プラグインと Wwise によって 2 回立体化されるのを回避するため、すべてのドライ バスへのユーザー定義の Aux 送信を無効にします。 サウンド バンクの再生成が必要であるため、立体化モードはリアルタイムで変更できません。 Unreal を再起動してから、再生を実行する前にサウンドバンクを再生成して、 **[Perform Spatialization]\(立体化の実行\)** チェック ボックス設定などのミキサー プラグインの構成変更を統合します。

![Wwise のミキサー プラグインの立体化設定](media/mixer-spatial-settings.png)

残念ながら、他のオブジェクトベースの立体化プラグインは現在サポートされていません。 これらはミキサー プラグインとして実装されています。Wwise では、複数のミキサー プラグインを 1 つのアクター ミキサーに割り当てることは許可されていません。  

### <a name="audio-setup-in-unreal"></a>Unreal でオーディオを設定する
1. まず、ゲーム レベルをベイクして、*Content\Acoustics* に配置される音響資産を生成します。 [Unreal ベイク チュートリアル](unreal-baking.md)を参照してください。 サンプル パッケージには、事前にベイクされたレベルがいくつか含まれています。

1. シーンに Acoustics Space アクターを作成します。 レベルにはこれらのアクターの 1 つのみを作成します。これがレベル全体の音響を表すからです。

    ![Unreal エディターでの Acoustics Space アクターの作成](media/create-acoustics-space.png)

1. ベイクされた音響データ資産を Acoustics Space アクター上の音響データ スロットに割り当てます。 シーンに音響が追加されました。

    ![Unreal エディターでの音響資産の割り当て](media/acoustics-asset-assign.png)

1. 空のアクターを追加します。 それを次のように構成します。

    ![空のアクターでの音響コンポーネントの使用を示している Unreal エディター](media/acoustics-component-usage.png)

       
    <sup>1</sup> アクターに音響オーディオ コンポーネントを追加します。 このコンポーネントにより、Project Acoustics の機能が Wwise オーディオ コンポーネントに追加されます。
        
    <sup>2</sup> 既定では、 **[Play on Start]\(開始時に再生\)** ボックスが選択されています。 この設定により、レベル起動時に関連付けられた Wwise イベントがトリガーされます。</li>
         
    <sup>3</sup> **[Show Acoustics Parameters]\(音響パラメーターの表示\)** チェック ボックスを使用して、ソースに関する画面上のデバッグ情報を出力します。

    ![デバッグ値が有効になっている音源の Unreal エディターの音響パネル](media/debug-values.png)

    <sup>4</sup> 通常の Wwise ワークフローごとに Wwise イベントを割り当てます。
       
    <sup>5</sup> **[Use Spatial Audio]\(空間オーディオの使用\)** がオフになっていることを確認します。 Project Acoustics を特定のオーディオ コンポーネントに使用する場合、同時に Wwise の空間オーディオ エンジンを音響に使用することはできません。</li>
       
設定が完了しました。 シーンを移動し、音響の効果を調べます。

## <a name="next-steps"></a>次の手順
* [Project Acoustics Unreal/Wwise のデザイン チュートリアル](unreal-workflow.md)を試します。
* ゲーム シーンに対して[ベイクを行う方法](unreal-baking.md)について学習します。
