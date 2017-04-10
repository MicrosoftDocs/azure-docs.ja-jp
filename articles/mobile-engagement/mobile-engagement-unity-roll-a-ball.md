---
title: "Unity Roll a Ball チュートリアル"
description: "Mobile Engagement Unity のすべてのチュートリアルの前提条件である、従来の Unity Roll a Ball ゲームを作成する手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2


---
# <a name="a-idunity-roll-a-ballacreate-unity-roll-a-ball-game"></a><a id="unity-roll-a-ball"></a>Unity Roll a Ball ゲームの作成
このチュートリアルでは、若干変更された [Unity Roll a Ball のチュートリアル](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)の主な手順について説明します。 このサンプル ゲームは、アプリ ユーザーによって操作される球形の "プレーヤー" オブジェクトで構成されます。ゲームの目的は、プレーヤー オブジェクトを収集可能なオブジェクトに衝突させることによって、このような収集可能なオブジェクトを "収集" することです。 このチュートリアルは、Unity エディター環境の基礎知識があることを前提としています。 問題が発生した場合は、完全なチュートリアルを参照してください。 

### <a name="setting-up-the-game"></a>ゲームを設定する
次の手順は、 [Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. **Unity エディター**を開き、**[New]** \(新規) をクリックします。 
   
    ![][51] 
2. **[Project name]** \(プロジェクト名) ボックスと  & **[Location]** \(場所) ボックスに適切な情報を入力し、**[3D]** \(3D) を選択して、**[Create project]** \(プロジェクトの作成) をクリックします。
   
    ![][52]
3. 新しいプロジェクトの一部として作成された既定のシーンを、**Assets** フォルダーの下の新しい **\_Scenes** フォルダーに **MiniGame** という名前で保存します。
   
    ![][53]
4. **[3D Object] \(3D オブジェクト)、[Plane] \(平面)** の順に選択して、プレイするフィールドとして平面を作成し、この平面オブジェクトの名前を **Ground** に変更します。
   
    ![][1]
5. この **Ground** オブジェクトの変換コンポーネントをリセットして、そのオブジェクトが原点に配置されるようにします。 
   
    ![][3]
6. **Ground** オブジェクトの **[Gizmos]** \(ギズモ) メニューにある **[Show Grid]** \(グリッドの表示) チェック ボックスをオフにします。
   
    ![][4]
7. **Ground** オブジェクトの **[Scale]** \(スケール) コンポーネントを [X = 2、Y = 1、Z = 2] に更新します。 
   
    ![][5]
8. **[3D Object] \(3D オブジェクト)、[Sphere] \(球)** の順に選択して新しいオブジェクトをプロジェクトに追加し、この球オブジェクトの名前を **Player** に変更します。 
   
    ![][6]
9. **Player** オブジェクトを選択し、平面オブジェクトの場合と同様に、**[Reset Transform]** \(変換のリセット) をクリックします。 
10. **[Transform] \(変換)、[Position] \(位置)、[Y] \(Y)** の順に選択して、Player の Y 座標コンポーネントを 0.5 に更新します。  
    
    ![][7]
11. **Materials** という名前の新しいフォルダーをプロジェクトに作成します。このフォルダーに、プレイヤーの色を設定するマテリアルを作成します。 
12. このフォルダー内に、**Background** という名前の新しい**マテリアル**を作成します。 
    
    ![][8]
13. マテリアルの色を更新するには、その **[Albedo]** \(アルベド) プロパティを更新します。 RGB 値 [0,32,64] を選択できます。 
    
    ![][9]
14. このマテリアルをシーン ビューにドラッグして、 **Ground** オブジェクトに色を適用します。 
    
    ![][10]
15. 最後に、わかりやすくするために、Directional Light オブジェクトで、**[Transform] \(変換) の [Rotation] \(回転) で [Y] \(Y)** を 60 に設定します。 
    
    ![][12]

### <a name="moving-the-player"></a>プレイヤーを動かす
次の手順は、 [Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. **RigidBody** コンポーネントを **Player** オブジェクトに追加します。 
   
    ![][13]
2. **Scripts** という名前の新しいフォルダーをプロジェクトに作成します。 
3. **[Add Component] \(コンポーネントの追加)、[New Script] \(新しいスクリプト)、[C# Script] \(C# スクリプト)** の順にクリックします。 スクリプトに **PlayerController** という名前を付け、**[Create and Add]** \(作成して追加) をクリックします。 これで、スクリプトが作成され、Player オブジェクトにアタッチされます。  
   
    ![][14]
4. このスクリプトをプロジェクトの **Scripts** フォルダーに移動します。 
5. 好みのスクリプト エディターでこのスクリプトを編集するために開き、スクリプト コードを次のコードで更新して、保存します。 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. 上記のスクリプトで **Speed** プロパティが使用されていることに注目します。 Unity エディターで、[Speed] \(速度) プロパティを 10 に更新します。  
   
    ![][15]
7. Unity エディターの **[Play]** \(再生) をクリックします。 これで、キーボードを使って球を操作できるようになります。球は回転したり、動き回ったりします。 

### <a name="moving-the-camera"></a>カメラを動かす
次の手順は、[Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141)に基づいています。この手順では、**Main Camera** を **Player** オブジェクトに関連付けます。 

1. **Transform.Position** を X = 0、Y = 10.5、Z=-10 に更新します。  
2. **Transform.Rotation** を X = 45、Y = 0、Z = 0 に更新します。  
   
    ![][16]
3. **CameraController** という名前の新しいスクリプトを **MainCamera** に追加し、そのスクリプトを Scripts フォルダーに移動します。 
   
    ![][17]
4. スクリプトを編集するために開き、次のコードを追加します。
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Unity 環境で、Player 変数を Main Camera オブジェクトの Player スロットにドラッグして、この 2 つを相互に関連付けます。 
   
    ![][18]
6. これで、エディターで [Play] \(再生) をクリックし、Player Ball オブジェクトを回転させると、カメラはその動きを追いかけます。  

### <a name="setting-up-the-play-area"></a>プレイ領域を設定する
次の手順は、 [Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141)に基づいています。 ここでは、Player Ball オブジェクトが動く際にプレイ領域から飛び出さないように Ground オブジェクトを取り囲む Walls オブジェクトを作成します。 

1. **[Create] \(作成)、[Create Empty] \(空のオブジェクト)、[Game Object] \(ゲーム オブジェクト)** の順にクリックし、そのオブジェクトに **Walls** という名前を付けます。
   
    ![][19]
2. この Walls オブジェクトの下で、**[3D Object] \(3D オブジェクト)、[Cube] \(キューブ)** の順に選択してオブジェクトを作成し、"West wall" という名前を付けます。 
   
    ![][20]
3. この West wall オブジェクトについて、**[Transform] \(変換) の [Position] \(位置)** と **[Transform] \(変換) の [Scale] \(スケール)** を更新します。 
   
    ![][21]
4. West wall を複製して **East wall** を作成し、変換の位置とスケールを更新します。 
   
    ![][22]
5. East wall を複製して **North wall** を作成し、変換の位置とスケールを更新します。 
   
    ![][23]
6. North wall を複製して **South wall** を作成し、変換の位置とスケールを更新します。 
   
    ![][24]

### <a name="creating-collectible-objects"></a>収集可能なオブジェクトを作成する
次の手順は、 [Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141)に基づいています。 ここでは、収集可能なオブジェクトのセットを構成する見栄えのよいオブジェクトをいくつか作成します。Player Ball オブジェクトは、これらのオブジェクトを衝突させることによって "収集" する必要があります。 

1. 新しい **3D キューブ オブジェクト** を作成し、Pickup という名前を付けます。 
2. この Pickup オブジェクトについて、**[Transform] \(変換) の [Rotation] \(回転)** と &  **[Transform] \(変換) の [Scale] \(スケール)** を調整します。 
   
    ![][25]
3. **Rotator** という名前の**新しい C# スクリプト**を作成し、Pickup オブジェクトにアタッチします。 このスクリプトを Scripts フォルダーの下に配置するようにしてください。 
   
    ![][26]
4. このスクリプトを編集するために開き、次のように更新します。 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. エディターで [Play] \(再生) をクリックすると、Pickup オブジェクトがその軸を中心に回転します。
6. **Prefabs** という名前の新しいフォルダーを作成します。 
   
    ![][27]
7. **Pickup** オブジェクトをドラッグして Prefabs フォルダーに置きます。
   
    ![][28]
8. **Pickups** という名前の新しい**空のゲーム オブジェクト**を作成します。 その位置を原点にリセットした後、このゲーム オブジェクトの下に Pickup オブジェクトをドラッグします。  
   
    ![][29]
9. **Pickup** オブジェクトを複製し、**Transform.Position の X と Z** の値を適宜更新して、**Player** オブジェクトを取り囲む **Ground** オブジェクト上に散らします。 
   
    ![][30]
10. **Pickup** という名前の**新しいマテリアル**を作成します。Ground オブジェクトを更新したときと同じように、**[Albedo] \(アルベド) プロパティ**を更新して、その色が [Red] \(赤) になるように更新します。 
    
    ![][31]
11. マテリアルを 4 つの Pickup オブジェクトすべてに適用します。
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Pickup オブジェクトを収集する
次の手順は、 [Unity のチュートリアル](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141)に基づいています。 ここでは、Player が衝突することで Pickup オブジェクトを "収集" できるように Player を更新します。 

1. Player オブジェクトにアタッチされている **PlayerController** スクリプトを編集するために開き、次のように更新します。  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. **Pick Up** という名前の新しい**タグ**を作成します (名前は、スクリプトの内容と一致する必要があります)。  
   
    ![][33]
   
    ![][34]
3. この **タグ** を Prefab Pickup オブジェクトに適用します。 
   
    ![][35]
4. Prefab オブジェクトの **[IsTrigger]** チェック ボックスをオンにします。
   
    ![][36]
5. RigidBody を Pickup Prefab オブジェクトに追加します。 ここでは、パフォーマンスを最適化するために、使用していた静的コライダーを動的コライダーに更新します。 
   
    ![][37]
6. 最後に、Prefab オブジェクトの **IsKinematic** プロパティのチェック ボックスをオンにします。 
   
    ![][38]
7. Unity エディターの **[Play]** \(再生) をクリックすると、方向入力にキーボードのキーを使用して Player オブジェクトを動かすことで、この **Roll a Ball** ゲームをプレイできるようになります。 

### <a name="updating-the-game-for-mobile-play"></a>モバイルでプレイできるようにゲームを更新する
Unity の基本的なチュートリアルは、上のセクションで終了です。 ここでは、モバイル デバイスで使いやすくなるようにゲームを変更します。 ここまでは、ゲームをテストする際にキーボード入力を使用してきたことに注意してください。 今度は、電話の所作を使用して (つまり、加速度計を入力として使用して)、 プレイヤーを操作できるように変更します。 

**PlayerController** スクリプトを編集するために開き、Player オブジェクトの移動に加速度計からの動きを使用するように **FixedUpdate** メソッドを更新します。 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

これで、Unity を使用した基本的なゲームの作成チュートリアルはおしまいです。このゲームを任意のデバイスにデプロイしてゲームをプレイすることができます。 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png
















<!--HONumber=Nov16_HO3-->


