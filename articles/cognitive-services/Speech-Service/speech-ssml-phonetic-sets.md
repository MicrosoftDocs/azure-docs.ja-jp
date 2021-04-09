---
title: Speech 発音設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス発音アルファベットを国際音標文字 (IPA) にマップする方法と、どのような設定をいつ使用するかについて説明します。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 580de0112c99eef4ba22ba946f6a2b81bc326001
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432045"
---
# <a name="speech-service-phonetic-sets"></a>Speech サービス発音設定

Speech サービスでは、次の 7 つの言語で構成される音声アルファベット (略語の"音声セット") を定義します。`en-US`、`fr-FR`、`de-DE`、`es-ES`、`ja-JP`、`zh-CN`、および `zh-TW`。 音声サービスの音標文字は通常、<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">国際音標文字 (IPA)</a> にマップされます。 Speech サービス音声セットでは、[Speech 合成マークアップ言語 (SSML)](speech-synthesis-markup.md)と共に、テキスト音声変換サービスオファリングの一部として使用されます。 この記事では、これらの音声セットがどのようにマップされているか、およびどのような音声セットをどのようなタイミングで使用するかについて説明します。

# <a name="en-us"></a>[ja-JP](#tab/en-US)

### <a name="english-suprasegmentals"></a>英語の超分節音素

| 例 1          (頭子音、語頭母音) | 例 2          (母音間子音、介音) | 例 3     (尾子音、語末母音) | コメント |
|--|--|--|--|
| burger  /b er **1** r - g ax r/ | falafel  /f ax - l aa **1** - f ax  l/ | guitar  /g ih - t aa **1** r/ | Speech サービス電話設定では、強勢音節の母音の後に強勢が置かれます |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilarity  /d ih - s ih **2**- m ax -  l eh 1 - r ax - t iy/ | workforce /w er 1 r k - f ao **2** r s/ | Speech サービス電話設定では、副強勢音節の母音の後に強勢が置かれます |

### <a name="english-vowels"></a>英語の母音

| `sapi` | `ipa` | 例 1     | 例 2 | 例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea** t       | f **ee** l  | vall **ey**                  |
| ih     | `ɪ`   | **i** f        | f **i** ll  |                             |
| ey     | `eɪ`  | **a** te       | g **a** te  | d **ay**                     |
| eh     | `ɛ`   | **e** very     | p **e** t   | m **eh** (まれな語末) |
| ae     | `æ`   | **a** ctive    | c **a** t   | n **ah** (まれな語末) |
| aa     | `ɑ`   | **o** bstinate | p **o** ppy | r **ah** (まれな語末) |
| ao     | `ɔ`   | **o** range    | c **au** se | Ut **ah**                    |
| uh     | `ʊ`   | b **oo** k      |           |                             |
| ow     | `oʊ`  | **o** ld       | cl **o** ne | g **o**                      |
| uw     | `u`   | **U** ber      | b **oo** st | t **oo**                     |
| ah     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| ay     | `aɪ`  | **i** ce       | b **i** te  | fl **y**                     |
| aw     | `aʊ`  | **ou** t       | s **ou** th | c **ow**                     |
| oy     | `ɔɪ`  | **oi** l       | j **oi** n  | t **oy**                     |
| y uw   | `ju`  | **Yu** ma      | h **u** man | f **ew**                     |
| ax     | `ə`   | **a** go       | wom **a** n | are **a**                    |

### <a name="english-r-colored-vowels"></a>英語の r 音性母音

| `sapi` | `ipa` | 例 1    | 例 2      | 例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ear** s     | t **ir** amisu   | n **ear**   |
| eh r   | `ɛɹ`  | **air** plane | app **ar** ently | sc **ar** e  |
| uh r   | `ʊɹ`  |              |                | c **ur** e   |
| ay r   | `aɪɹ` | **Ire** land  | f **ir** eplace  | ch **oir**  |
| aw r   | `aʊɹ` | **hour** s    | p **ower** ful   | s **our**   |
| ao r   | `ɔɹ`  | **or** ange   | m **or** al      | s **oar**   |
| aa r   | `ɑɹ`  | **ar** tist   | st **ar** t      | c **ar**    |
| er r   | `ɝ`   | **ear** th    | b **ir** d       | f **ur**    |
| ax r   | `ɚ`   |              | all **er** gy    | supp **er** |

### <a name="english-semivowels"></a>英語の半母音

| `sapi` | `ipa` | 例 1           | 例 2  | 例 3 |
|--------|-------|---------------------|------------|-----------|
| 。      | `w`   | **w** ith、s **ue** de | al **w** ays |           |
| ○      | `j`   | **y** ard、f **e** w   | on **i** on  |           |

### <a name="english-aspirated-oral-stops"></a>英語の有気閉鎖音

| `sapi` | `ipa` | 例 1 | 例 2   | 例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** ut   | ha **pp** en  | fla **p**   |
| b      | `b`   | **b** ig   | num **b** er  | cra **b**   |
| t      | `t`   | **t** alk  | capi **t** al | sough **t** |
| d      | `d`   | **d** ig   | ran **d** om  | ro **d**    |
| k      | `k`   | **c** ut   | sla **ck** er | Ira **q**   |
| G      | `g`   | **g** o    | a **g** o     | dra **g**   |

### <a name="english-nasal-stops"></a>英語の鼻腔閉鎖音

| `sapi` | `ipa` | 例 1        | 例 2  | 例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m** at、smash   | ca **m** era | roo **m**    |
| n      | `n`   | **n** o、s **n** ow | te **n** t   | chicke **n** |
| ng     | `ŋ`   |                  | li **n** k   | s **ing**    |

### <a name="english-fricatives"></a>英語の摩擦音

| `sapi` | `ipa` | 例 1   | 例 2        | 例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | le **f** t         | hal **f**   |
| v      | `v`   | **v** alue   | e **v** ent        | lo **v** e   |
| th     | `θ`   | **th** in    | empa **th** y      | mon **th**  |
| dh     | `ð`   | **th** en    | mo **th** er       | smoo **th** |
| s      | `s`   | **s** it     | ri **s** k         | fact **s**  |
| z      | `z`   | **z** ap     | bu **s** y         | kid **s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia **ti** on | ru **sh**   |
| zh     | `ʒ`   | **J** acques | plea **s** ure     | gara **g** e |
| h      | `h`   | **h** elp    | en **h** ance      | a-**h** a!  |

### <a name="english-affricates"></a>英語の破擦音

| `sapi` | `ipa` | 例 1 | 例 2    | 例 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch** in  | fu **t** ure   | atta **ch** |
| jh     | `dʒ`  | **j** oy   | ori **g** inal | oran **g** e |

### <a name="english-approximants"></a>英語の接近音

| `sapi` | `ipa` | 例 1          | 例 2  | 例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** id、g **l** ad  | pa **l** ace | chi **ll** |
| r      | `ɹ`   | **r** ed、b **r** ing | bo **rr** o | ta **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>フランス語の超分節音素

Speech サービス電話設定では、強制音節の母音の後に強勢が置かれますが、`fr-FR` Speech サービス電話設定は IPA 副強勢 ' ˌ ' をサポートしていません。 IPA 副強勢が必要な場合は、IPA を直接使用する必要があります。

### <a name="french-vowels"></a>フランス語の母音

| `sapi` | `ipa` | 例 1     | 例 2       | 例 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **a** rbre     | p **a** tte       | ir **a**   |
| aa     | `ɑ`   |               | p **â** te        | p **a** s   |
| aa ~   | `ɑ̃`  | **en** fant    | enf **en** t      | t **em** ps |
| ax     | `ə`   |               | p **e** tite      | l **e**    |
| eh     | `ɛ`   | **e** lle      | p **e** rdu       | ét **ai** t |
| eu     | `ø`   | **œu** fs      | cr **eu** ser     | qu **eu**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im** portant | p **ein** ture    | mat **in** |
| iy     | `i`   | **i** dée      | pet **i** te      | am **i**   |
| oe     | `œ`   | **œu** f       | p **eu** r        |           |
| oh     | `ɔ`   | **o** bstacle  | c **o** rps       |           |
| oh ~   | `ɔ̃`  | **on** ze      | r **on** deur     | b **on**   |
| ow     | `o`   | **au** diteur  | b **eau** coup    | p **ô**    |
| oe ~   | `œ̃ ` | **un**        | l **un** di       | br **un**  |
| uw     | `u`   | **ou** trage   | intr **ou** vable | **ou**    |
| uy     | `y`   | **u** ne       | p **u** nir       | él **u**   |

### <a name="french-consonants"></a>フランス子音

| `sapi` | `ipa` | 例 1   | 例 2     | 例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | ha **b** ille   | ro **b** e                         |
| d      | `d`   | **d** ire    | ron **d** eur   | chau **d** e                       |
| f      | `f`   | **f** emme   | su **ff** ixe   | bo **f**                          |
| G      | `g`   | **g** auche  | é **g** ale     | ba **gu** e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)park **ing** |
| hy     | `ɥ`   | h **u** ile   | n **u** ire     |                                  |
| k      | `k`   | **c** arte   | é **c** aille   | be **c**                          |
| l      | `l`   | **l** ong    | é **l** ire     | ba **l**                          |
| m      | `m`   | **m** adame  | ai **m** er     | po **mm** e                        |
| n      | `n`   | **n** ous    | te **n** ir     | bo **nn** e                        |
| nj     | `ɲ`   |             |               | pei **gn** e                       |
| p      | `p`   | **p** atte   | re **p** as     | ca **p**                          |
| r      | `ʁ`   | **r** at     | cha **r** iot   | senti **r**                       |
| s      | `s`   | **s** ourir  | a **ss** ez     | pa **ss** e                        |
| sh     | `ʃ`   | **ch** anter | ma **ch** ine   | po **ch** e                        |
| t      | `t`   | **t** ête    | ô **t** er      | ne **t**                          |
| v      | `v`   | **v** ent    | in **v** enter  | rê **v** e                         |
| 。      | `w`   | **ou** i     | f **ou** ine    |                                  |
| ○      | `j`   | **y** od     | p **i** étiner  | Marse **ille**                    |
| z      | `z`   | **z **éro   | rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** ardin  | man **g** er    | piè **g** e                        |
|        | `n‿`  |             |               | u **n** arbre                     |
|        | `t‿`  |             |               | quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *一部の外国語のみに適用されます。*

> [!TIP]
> `fr-FR` Speech サービス電話設定は、次のフランス語のリエゾン、`n‿`、`t‿`、および `z‿` をサポートしていません。 必要に応じて、IPA を直接使用することを検討してください。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>ドイツ語の超分節音素

| 例 1          (頭子音、語頭母音) | 例 2          (母音間子音、介音) | 例 3     (尾子音、語末母音) | コメント |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Speech サービス電話設定では、強勢音節の母音の後に強勢が置かれます |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m  a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Speech サービス電話設定では、副強勢音節の母音の後に強勢が置かれます |

### <a name="german-vowels"></a>ドイツ語の母音

| `sapi` | `ipa`     | 例 1                             | 例 2     | 例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **A** ber                              | Maßst **a** b   | Schem **a**                         |
| a      | `a`       | **A** bfall                            | B **a** ch      | Agath **a**                         |
| oh     | `ɔ`       | **O** sten                             | Pf **o** sten   |                                    |
| eh :    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1)Fasci **ae** |
| eh     | `ɛ`       | **ä** ndern                            | Proz **e** nt   | Amygdal **ae**                      |
| ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **I** ran                              | abb **ie** gt   | Relativitätstheor **ie**            |
| ih     | `ɪ`       | **I** nnung                            | s **i** ngen    | Wood **y**                          |
| eu     | `øː`      | **Ö** sen                              | abl **ö** sten  | Malm **ö**                          |
| ow     | `o`, `oː` | **o** hne                              | Balk **o** n    | Trept **ow**                        |
| oe     | `œ`       | **Ö** ffnung                           | bef **ö** rdern |                                    |
| ey     | `e`, `eː` | **E** berhard                          | abf **e** gt    | b                                  |
| uw     | `uː`      | **U** do                               | H **u** t       | Akk **u**                           |
| uh     | `ʊ`       | **U** nterschiedes                     | b **u** nt      |                                    |
| ue     | `yː`      | **Ü** bermut                           | pfl **ü** gt    | Men **ü**                           |
| uy     | `ʏ`       | **ü** ppig                             | S **y** stem    |                                    |

<a id="de-v-1"></a>
**1** *次のような外来語のみ: Fasci **ae**。*<br>
<a id="de-v-2"></a>
**2** *次のような外来語のみの語頭:**A** ppointment。音節頭:  'v **e** rstauen。*

### <a name="german-diphthong"></a>ドイツ語の二重母音

| `sapi` | `ipa`       | 例 1    | 例 2          | 例 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei** nsam   | Unabhängigk **ei** t | Abt **ei** |
| aw     | `au`        | **au** ßen    | abb **au** st        | St **au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu** phorie | tr **äu** mt         | sch **eu** |

### <a name="german-semivowels"></a>ドイツ語の半母音

| `sapi` | `ipa` | 例 1 | 例 2    | 例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd **er** n | lock **er** |

### <a name="german-consonants"></a>ドイツ語の子音

| `sapi` | `ipa` | 例 1 | 例 2 | 例 3 |
|--|--|--|--|--|
| b | `b` | **B** ank |  | [<sup>1</sup>](#de-c-1)Pu **b** |  |
| c | `ç` | **Ch** emie | mögli **ch** st | [<sup>2</sup>](#de-c-2)i **ch** |
| d | `d` | **d** anken | [<sup>3</sup>](#de-c-3)Len **d** l | [<sup>4</sup>](#de-c-4)Clau **d** e |  |
| jh | `ʤ` | **J** eff | gemana **g** t | [<sup>5</sup>](#de-c-5)Chan **g** e |
| f | `f` | **F** ahrtdauer | angri **ff** slustig | abbruchrei **f** |  |
| G | `g` | **g** ut |  | [<sup>6</sup>](#de-c-6)Gre **g** |  |
| h | `h` | **H** ausanbau |  |  |  |
| ○ | `j` | **J** od | Reakt **i** on | hu **i** |  |
| k | `k` | **K** oma | Aspe **k** t | Flec **k** |  |
| l | `l` | **l** au | ähne **l** n | zuvie **l** |  |
| m | `m` | **M** ut | A **m** t | Leh **m** |  |
| n | `n` | **n** un | u **n** d | Huh **n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng** uyen | Schwa **nk** | R **ing** |  |
| p | `p` | **P** artner | abru **p** t | Ti **p** |  |
| pf | `pf` | **Pf** erd | dam **pf** t | To **pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R** eise | knu **rr** t | Haa **r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | bi **s** t | mie **s** |  |
| sh | `ʃ` | **Sch** ule | mi **sch** t | lappi **sch** |  |
| t | `t` | **T** raum | S **t** raße | Mu **t** |  |
| ts | `ts` | **Z** ug | Ar **z** t | Wit **z** |  |
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** |  |
| v | `v` | **w** inken | Q **u** alle | [<sup>9</sup>](#de-c-9)Gr **oo** ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`、[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)Ba **ch** erach | Ma **ch** t mögli **ch** st | Schma **ch** 'i **ch** |
| z | `z` | **s** uper |  |  |  |
| zh | `ʒ` | **G** enre | B **re** ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *次のような外来語のみ: Pu **b**。*<br>
<a id="de-c-2"></a>
**2** *'e' と 'i' の後の弱い 'ch'*<br>
<a id="de-c-3"></a>
**3** *次のような外来語のみ: Len **d** l。*<br>
<a id="de-c-4"></a>
**4** *次のような外来語のみ: Clau **d** e。*<br>
<a id="de-c-5"></a>
**5** *次のような外来語のみ: Chan **g** e。*<br>
<a id="de-c-6"></a>
**6** *次のような外来語のみの語末: Gre **g**。*<br>
<a id="de-c-7"></a>
**7** *次のような外来語のみ: **Ng** uyen。*<br>
<a id="de-c-8"></a>
**8** *次のような外来語のみ: **S** taccato。*<br>
<a id="de-c-9"></a>
**9** *次のような外来語のみ: Gr **oo** ve。*<br>
<a id="de-c-10"></a>
**10** *IPA `x` は、すべての非前舌母音 (a、aa、oh、ow、uh、uw、および二重母音 aw) の後の強い 'ch'です。*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` は、前舌母音 (ih、iy、eh、ae、uy、ue、oe、eu、および二重母音 ay、oy) と子音の後の弱い 'ch' です*<br>
<a id="de-c-12"></a>
**12** *次のような外来語のみの語頭: **J** uan。次のような語の音節頭も: Ba **ch** erach。*<br>

### <a name="german-oral-consonants"></a>ドイツ語の口音

| `sapi` | `ipa` | 例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich     /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> 2 つの母音が純粋な二重母音である場合を除き、2 つの異なる母音の間に [gs\] 音素を追加する必要があります。 この口頭の子音は声門閉鎖音です。詳細については、「<a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">声門閉鎖音 <span class="docon docon-navigate-external x-hidden-focus"></a></a>」を参照してください。

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>スペイン語の母音

| `sapi` | `ipa` | 例 1    | 例 2     | 例 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **a** lto     | c **a** ntar    | cas **a**     |
| i      | `i`   | **i** bérica  | av **i** spa    | tax **i**     |
| e      | `e`   | **e** lefante | at **e** nto    | elefant **e** |
| o      | `o`   | **o** caso    | enc **o** ntrar | ocasenc **o** |
| u      | `u`   | **u** sted    | p **u** nta     | Juanl **u**   |

### <a name="spanish-consonants"></a>スペイン語の子音

| `sapi` | `ipa`      | 例 1  | 例 2      | 例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | am **b**        |
|        | `β`        |            | bao **b** ab     | baoba **b**     |
| ch     | `tʃ`       | **ch** eque | co **ch** e      | Marraque **ch** |
| d      | `d`        | **d** edo   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | verda **d**     |
| f      | `f`        | **f** ácil  | ele **f** ante   | pu **f**        |
| G      | `g`        | **g** anga  |                | dópin **g**     |
|        | `ɣ`        |            | a **g** ua       | tuare **g**     |
| j      | `j`        | **i** odo   | cal **i** ente   | re **y**        |
| jj     | `j.j` `jj` |            | vi **ll** a      |                |
| k      | `k`        | **c** oche  | bo **c** a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | a **l** a        | corde **l**     |
| ll     | `ʎ`        | **ll** ave  | desarro **ll** o |                |
| m      | `m`        | **m** order | a **m** ar       | álbu **m**      |
| n      | `n`        | **n** ada   | ce **n** a       | rató **n**      |
| nj     | `ɲ`        | **ñ** aña   | ara **ñ** azo    |                |
| p      | `p`        | **p** oca   | to **p** o       | sto **p**       |
| r      | `ɾ`        |            | ca **r** a       | abri **r**      |
| rr     | `r`        | **r** adio  | co **rr** e      | pu **rr**       |
| s      | `s`        | **s** aco   | va **s** o       | pelo **s**      |
| t      | `t`        | **t** oldo  | a **t** ar       | disque **t**    |
| th     | `θ`        | **z** ebra  | a **z** ul       | lápi **z**      |
| 。      | `w`        | h **u** eso  | ag **u** a       | gua **u**       |
| x      | `x`        | **j** ota   | a **j** o        | relo **j**      |

> [!TIP]
> `es-ES` Speech サービス音声セットは、次のスペイン語 IPA、`β`、`ð`、および `ɣ` をサポートしていません。 必要に応じて、IPA を直接使用することを検討してください。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

`zh-CN` の音声サービスの音標文字は、固有の音標文字 <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin</a> に基づいています。

### <a name="tone"></a>[色調]

| Pinyin トーン | `sapi` | 文字の例 |
|-------------|--------|-------------------|
| mā          | ma  1  | 妈                 |
| má          | ma  2  | 麻                 |
| mǎ          | ma  3  | 马                 |
| mà          | ma  4  | 骂                 |
| ma          | ma  5  | 嘛                 |

#### <a name="example"></a>例

| 文字 | Speech サービス                |
|-----------|-------------------------------|
| 组织关系      | zu  3 - zhi 1 - guan 1 - xi 5 |
| 累进        | lei  3 -jin 4                 |
| 西宅巷       | xi  1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

`zh-TW` の音声サービスの音標文字は、固有の音標文字 <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo</a> に基づいています。

### <a name="tone"></a>[色調]

| Speech サービスのトーン | Bopomofo トーン | 例     (語) | Speech サービスの電話 | Bopomofo | Pinyin （拼音） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | 空         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ  ㄗ˙               | 一ㄥˇ  ㄗ˙  | yǐng  zi    |

#### <a name="example"></a>例

| 文字 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[en-US](#tab/ja-JP)

`ja-JP` の音声サービスの音標文字は、固有の音標文字 <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana</a> に基づいています。

### <a name="stress"></a>ストレス テスト

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` 強勢 |
| `+`    | `ˌ` 副強勢  |

#### <a name="example"></a>例

| 文字 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | goˈwɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ+  | sajitecikaˌ |

***