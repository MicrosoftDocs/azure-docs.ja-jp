---
title: Project Acoustics Unreal と Wwise の統合
titlesuffix: Azure Cognitive Services
description: ここでは、Project Acoustics Unreal および Wwise プラグインのプロジェクトへの統合について説明します。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 6207808efb9bee327afd2de21ffa59535acf4e55
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704804"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal と Wwise の統合
ここでは、既存の Unreal と Wwise のゲーム プロジェクトへの Project Acoustics プラグイン パッケージの詳しい統合手順を示します。 

ソフトウェア要件:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 または 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1\*
* [Unreal 用の Wwise プラグイン](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Wwise Unreal プラグインを使用する代わりに Wwise SDK の直接統合を使用する場合は、Project Acoustics Unreal プラグインを調べて、Wwise API 呼び出しを調整します。

Wwise 以外のオーディオ エンジンと共に Project Acoustics を使用する場合は、[Project Acoustics ディスカッション フォーラム](https://github.com/microsoft/ProjectAcoustics/issues)で機能強化要求を行ってください。 Project Acoustics Unreal プラグインを使用して音響データのクエリを実行してから、エンジンに対して API 呼び出しを行うことができます。

## <a name="download-project-acoustics"></a>Project Acoustics のダウンロード
まだ行っていない場合は、[Project Acoustics Unreal & Wwise プラグイン パッケージ](https://www.microsoft.com/download/details.aspx?id=58090)をダウンロードします。 

Unreal Engine プラグインと Wwise ミキサー プラグインをパッケージに含めています。 Unreal プラグインでは、エディターとランタイムの統合が提供されます。 ゲームプレイ中に、Project Acoustics Unreal プラグインでは、フレームごとの各ゲーム オブジェクトのオクルージョンなどのパラメーターが計算されます。 これらのパラメーターは、Wwise API 呼び出しに変換されます。

## <a name="review-integration-steps"></a>統合手順の確認

パッケージをインストールしてゲームにデプロイするための主な手順は次のとおりです。
1. Project Acoustics Wwise ミキサー プラグインをインストールする
2. ゲームに Wwise を (再) デプロイする。 この手順では、ゲーム プロジェクトにミキサー プラグインが伝達されます。
3. ゲームに Project Acoustics Unreal プラグインを追加する
4. Wwise の Unreal プラグイン機能を拡張する
5. ゲームを作成し、Python が有効になっていることを確認する
6. Project Acoustics を使用するように Wwise プロジェクトを設定する
7. Unreal でオーディオを設定する

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1.Project Acoustics ミキサー プラグインをインストールする
* Wwise Launcher を開き、 **[Plugins]\(プラグイン\)** タブの **[Install New Plugins]\(新しいプラグインのインストール\)** で、 **[Add From Directory]\(ディレクトリから追加\)** を選択します。 

    ![Wwise ランチャーへのプラグインのインストールのスクリーンショット](media/wwise-install-new-plugin.png)

* ダウンロードしたパッケージに含まれた `AcousticsWwisePlugin\ProjectAcoustics` ディレクトリを選択します。 Wwise ミキサー プラグインのバンドルが含まれています。

* Wwise によってプラグインがインストールされます。 Wwise にインストールされているプラグインの一覧に Project Acoustics が表示されます。
![Project Acoustics インストール後のインストールされた Wwise プラグインの一覧のスクリーンショット](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2.ゲームに Wwise を (再) デプロイする
Wwise を既に統合している場合でも、ゲームに Wwise を再デプロイします。 これにより、Project Acoustics Wwise プラグインが取得されます。

* **エンジン プラグイン:** Wwise を Unreal C++ プロジェクトにゲーム プラグインとしてインストールした場合は、この手順をスキップします。 たとえば Unreal プロジェクトがブループリント専用であるため Wwise をエンジン プラグインとしてインストールした場合、ミキサー プラグインを含む Wwise のデプロイはより複雑になります。 空のダミー Unreal C++ プロジェクトを作成し、Unreal エディターで開いている場合は閉じて、残りの手順に従って Wwise をこのダミー プロジェクトにデプロイします。 次に、デプロイされた Wwise プラグインをコピーします。
 
* Wwise Launcher から、 **[Unreal Engine]** タブをクリックし、 **[Recent Unreal Engine Projects]\(最近使った Unreal Engine プロジェクト\)** の横にあるハンバーガー メニューをクリックして、 **[Browse for project]\(プロジェクトの参照\)** を選択します。 ゲームの Unreal プロジェクト `.uproject` ファイルを開きます。

    ![Wwise ランチャーの Unreal タブのスクリーンショット](media/wwise-unreal-tab.png)

* 次に、 **[Integrate Wwise in Project]\(プロジェクトでの Wwise の統合\)** または **[Modify Wwise in Project]\(プロジェクトでの Wwise の変更\)** をクリックします。 この手順では、Project Acoustics ミキサー プラグインを含むようになったプロジェクトに Wwise バイナリを (再) 統合します。

* **エンジン プラグイン:** Wwise をエンジン プラグインとして Wwise を使用し、上記のようにダミー プロジェクトを作成した場合は、Wwise によってデプロイされたフォルダー: `[DummyUProject]\Plugins\Wwise` をコピーし、`[UESource]\Engine\Plugins\Wwise` に貼り付けます。 `[DummyUProject]` は空の Unreal C++ プロジェクト パスで、`[UESource]` は Unreal Engine のソースをインストールした場所です。 コピーが完了したら、ダミー プロジェクトを削除することができます。

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>手順 3.ゲームに Project Acoustics Unreal プラグインを追加する
 
* プラグイン パッケージ内の `Unreal\ProjectAcoustics` フォルダーをコピーし、新しいフォルダー `[UProjectDir]\Plugins\ProjectAcoustics` を作成します。ここで、`UProjectDir` は `.uproject` ファイルを含むゲームのプロジェクト フォルダーです。
  * **エンジン プラグイン**:Wwise をエンジン プラグインとして使用している場合は、Project Acoustics を Unreal のエンジン プラグインとして使用する必要もあります。 上記のコピー先ディレクトリの代わりに、`[UESource]\Engine\Plugins\ProjectAcoustics` を使用します。

* `Wwise` フォルダーが `ProjectAcoustics` フォルダーと共に表示されることを確認します。 上記の手順 2 で (再) デプロイしたミキサー プラグインのバイナリと共に Wwise プラグインが含まれています。

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4.Wwise の Unreal プラグイン機能を拡張する
* Project Acoustics Unreal プラグインでは、[こちらのガイドライン](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)に従って Wwise Unreal プラグイン API から追加動作を公開する必要があります。 修正プログラムの適用の手順を自動化するバッチ ファイルを含めました。 
* `Plugins\ProjectAcoustics\Resources` 内で、`PatchWwise.bat` を実行します。 次の画像の例では、この AcousticsGame サンプル プロジェクトを使用しています。

    ![Wwise を修正するために提供されたスクリプトが強調表示されている Windows エクスプローラー ウィンドウのスクリーンショット](media/patch-wwise-script.png)

* DirectX SDK がインストールされていない場合は、`[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` の DXSDK_DIR を含む行をコメントアウトする必要があります

    ![DXSDK がコメント アウトされているコード エディターのスクリーンショット](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5.ゲームを作成し、Python が有効になっていることを確認する

* ゲームをコンパイルし、正しくビルドされることを確認します。 そうでない場合は、先に進む前に前の手順を慎重に確認してください。 
* Unreal エディターでプロジェクトを開きます。 
* **エンジン プラグイン:** ProjectAcoustics をエンジン プラグインとして使用している場合は、それが有効になっていて、「組み込み」プラグインの下に表示されていることも確認します。
* Project Acoustics が統合されたことを示す新しいモードも表示されます。

    ![Acoustics Mode Full を示している Unreal のスクリーンショット](media/acoustics-mode-full.png)

* Unreal の Python プラグインが有効になっていることを確認します。 これは、エディター統合が正しく機能するために必要です。

    ![Unreal エディターでの Python 拡張機能の有効化のスクリーン ショット](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6.Wwise プロジェクトを設定する

Wwise プロジェクトの例は、サンプル ダウンロードに含まれています。 これらの手順と並行して参照することをお勧めします。 以下のスクリーン ショットは、このプロジェクトから取得されたものです。

### <a name="bus-setup"></a>バスのセットアップ
* Project Acoustics Unreal プラグインでは、次の "***正確***" な名前のバス上で、関連付けられているミキサー プラグインが検索されます: `Project Acoustics Bus`。 この名前の新しいオーディオ バスを作成してください。 ミキサー プラグインはさまざまな構成で動作できますが、ここではリバーブ処理のみを行うために使用されることを前提としています。 このバスでは、音響を使用するすべてのソースに対してミキシングされたリバーブ信号が搬送されます。 次の例に示すように、サンプル ダウンロードに含まれる Wwise サンプル プロジェクトから取得された任意のバス ミキシング構造にアップストリームをミキシングできます。

    ![Project Acoustics Bus を示している Wwise バスのスクリーンショット](media/acoustics-bus.png)

* バス上のチャネル構成は、次のいずれかに設定する必要があります: `1.0, 2.0, 4.0, 5.1 or 7.1`。 その他の構成では、このバスへの出力はありません。

    ![Project Acoustics Bus のチャネル構成オプションのスクリーンショット](media/acoustics-bus-channel-config.png)

* 次に、Project Acoustics バスの詳細に進み、[ミキサー プラグイン] タブが表示されることを確認します

    ![Project Acoustics Bus の [ミキサー プラグイン] タブを有効にする方法を示している Wwise のスクリーンショット](media/mixer-tab-enable.png)

* [ミキサー プラグイン] タブに移動し、Project Acoustics ミキサー プラグインをバスに追加します

    ![Project Acoustics ミキサー プラグインの追加方法を示している Wwise バスのスクリーンショット](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>アクターミキサー階層のセットアップ
* パフォーマンス上の理由から、Project Acoustics ではオーディオ DSP がすべてのソースに同時に適用されます。 これには、プラグインがミキサー プラグインとして動作することが必要です。 通常、出力バスはドライ出力信号を搬送しますが、Wwise ではミキサー プラグインが出力バス上に存在する必要があります。 Project Acoustics では、ドライ信号が Aux バスを介してルーティングされる一方、ウェット信号が `Project Acoustics Bus` 上で実行される必要があります。 次のプロセスでは、この信号フローへの段階的な移行がサポートされます。

* 足音、武器、その他を最上位レベルに含むアクターミキサー階層を持つ既存のプロジェクトがあるとします。 それぞれに、そのドライ ミックスに対応する出力バスがあります。 音響を使用するように足音を移行するとしましょう。 まず、足音出力バスの子であるドライ サブミックスを搬送するために、対応する Aux バスを作成します。 たとえば、次の図では "Dry" プレフィックスを使用してこれらを整理しましたが、正確な名前は重要ではありません。 足音バスに対する測定や効果は以前と同様に機能します。

    ![推奨される Wwise の Dry ミックス設定のスクリーンショット](media/wwise-dry-mix-setup.png)

* 次に、Project Acoustics Bus を出力バスとして設定し、Dry_Footsteps をユーザー定義の Aux バスとして設定して、足音のアクターミキサーのバスの出力構造を次のように変更します。

    ![推奨される Wwise のアクター ミキサー バスの設定のスクリーンショット](media/actor-mixer-bus-settings.png)

* ここで、すべての足音が音響処理され、Project Acoustics Bus にリバーブを出力します。 ドライ信号は Dry_Footsteps 経由でルーティングされ、通常どおり立体化されます。

* Project Acoustics は、世界に 3D の場所を持つサウンドにのみ適用されます。 [Wwise ドキュメント](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)に従って、示されているように配置プロパティを設定する必要があります。 [3D Spatialization]\(3D 立体化\) 設定は、必要に応じて [Position]\(位置\) または [Position + Orientation]\(位置 + 方向\) にすることができます。

    ![推奨される Wwise のアクター ポジション設定のスクリーンショット](media/wwise-positioning.png)

* 出力バスを、アップ ストリームを **Project Acoustics Bus** にミキシングする他のバスに設定しても、機能しません。 Wwise では、ミキサー プラグインにこの要件が課されます。

* 足音アクターミキサー階層内の子に音響を使用しない場合は、いつでも "親のオーバーライド" を使用してオプトアウトできます。

* ゲームまたはユーザーが定義した送信をゲームのアクターミキサー上のリバーブに使用している場合は、リバーブを 2 回適用しないようにこのアクターミキサー上でオフにします。

### <a name="spatialization"></a>立体化
既定では、Project Acoustics Wwise ミキサー プラグインではコンボリューション リバーブが適用され、Wwise にはパン立体化が残されます。 この既定のリバーブのみの構成で Project Acoustics を使用している場合は、ドライ ミックスに対して任意のチャネル構成と立体化方法を自由に使用でき、ほぼ任意の立体化処理を Project Acoustics のリバーブとミキシングし、一致させることができます。 オプションには、[Ambisonics ベースのバイノーラル立体化処理](https://www.audiokinetic.com/products/ambisonics-in-wwise/)と [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) が含まれます。
 
Project Acoustics には、オブジェクトベースの高解像度 HRTF レンダリングとパンの両方をサポートするオプションの立体化処理が含まれています。 ミキサー プラグイン設定で、[立体化の実行] チェックボックスをオンにし、HRTF またはパンを選択し、上ですべてのドライ バスに設定したユーザー定義の Aux 送信を無効にして、Project Acoustics ミキサー プラグインと Wwise の両方で 2 回立体化することを回避します。 サウンド バンクの再生成が必要であるため、立体化モードはリアルタイムで変更できません。 Unreal を再起動してから、再生を実行する前にサウンドバンクを再生成して、[立体化の実行] チェックボックスなどのミキサー プラグインの構成変更を選択する必要があります。

![Wwise のミキサー プラグインの立体化設定のスクリーンショット](media/mixer-spatial-settings.png)

残念ながら、他のオブジェクトベースの立体化処理プラグインは、ミキサー プラグインとして実装されており、Wwise では現在 1 つのアクターミキサーに複数のミキサー プラグインを割り当てることができないため、現時点ではサポートされていません。  

## <a name="7-audio-setup-in-unreal"></a>7.Unreal でオーディオを設定する
* まず、ゲーム レベルをベイクして、`Content\Acoustics` に配置される音響資産を生成します。 [Unreal ベイク チュートリアル](unreal-baking.md)を調べて、ここで再開します。 サンプル パッケージには、事前にベイクされたレベルがいくつか含まれています。
* シーンに Acoustics Space アクターを作成します。 レベルにはこれらのアクターの 1 つのみを作成します。これはレベル全体の音響を表すためです。 

    ![Acoustics Space アクターの作成を示している Unreal エディターのスクリーンショット](media/create-acoustics-space.png)

* ここで、ベイクされた音響データ資産を Acoustics Space アクター上の音響データ スロットに割り当てます。 シーンに音響が追加されました。

    ![音響資産の割り当てを示している Unreal エディターのスクリーンショット](media/acoustics-asset-assign.png)

* ここで、空のアクターを追加し、次の操作を行います。

    ![空のアクターでの音響コンポーネントの使用を示している Unreal エディターのスクリーンショット](media/acoustics-component-usage.png)

1. アクターに音響オーディオ コンポーネントを追加します。 このコンポーネントは、Wwise オーディオ コンポーネントを Project Acoustics の機能で拡張します。
2. 既定では、レベル起動時に関連付けらている Wwise イベントをトリガーする [Play on Start]\(開始時に再生\) ボックスがオンになります。
3. [Show Acoustics Parameters]\(音響パラメーターの表示\) チェックボックスを使用して、ソースに関する画面上のデバッグ情報を出力します。
    ![デバッグ値が有効になっている音源の Unreal エディターの音響パネルのスクリーンショット](media/debug-values.png)
4. 通常の Wwise ワークフローごとに Wwise イベントを割り当てます
5. [Use Spatial Audio]\(空間オーディオの使用\) がオフになっていることを確認します。 この時点で、Project Acoustics を特定のオーディオ コンポーネントに使用する場合、同時に Wwise の空間オーディオ エンジンを音響に使用することはできません。

設定が完了しました。 シーンを移動し、音響の効果を調べます。

## <a name="next-steps"></a>次の手順
* Unreal/Wwise での Project Acoustics の[デザイン](unreal-workflow.md) チュートリアル
* ゲーム シーンに対して[ベイクを行う方法について](unreal-baking.md) 
