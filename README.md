# **ヒューマノイドロボットの技術的進化に関する包括的研究報告書：2023年から2025年におけるボディメカニクスと制御パラダイムの転換**

## **1\. 序論：ヒューマノイドロボティクスにおける「形態と機能」の変遷**

2020年代半ば、ヒューマノイドロボット技術は、研究開発段階から商用化を見据えた実用段階へと急速な変貌を遂げている。特に2023年から2025年にかけての期間は、二足歩行メカニズムにおいて根本的な設計思想の転換点として位置づけられる。本レポートは、ユーザーが提示した仮説「2023年は膝支点/足首なし、2025年は骨盤支点/足首・股関節あり」という観察に基づき、その技術的背景、力学的必然性、そして制御理論の進化について、詳細かつ体系的な分析を行うものである。

従来のヒューマノイドロボット（2023年時点の主要モデル群）は、安定性を最優先するために人間とは異なる特異な歩行様式、いわゆる「膝を曲げた状態での歩行（Bent-knee walking）」を採用していた。これは、線形倒立振子モード（Linear Inverted Pendulum Mode: LIPM）などの単純化された制御モデルにロボットの物理挙動を適合させるための工学的妥協であった 1。この時期のロボットにおいて、足首は推進器ではなく、単なる接地点の微調整役（受動的スタビライザー）として機能していた 3。

対照的に、2025年に向けて開発が進む次世代機（Tesla Optimus Gen 2以降、Unitree G1/H1、電動Atlasなど）は、生体模倣（バイオミメティクス）のアプローチを深め、骨盤（Pelvis）の回旋運動と能動的な足首・股関節の駆動を統合した「直立歩行（Straight-leg walking）」を実現している。この進化は、単なるアクチュエータの高性能化だけでなく、モデル予測制御（MPC）から深層強化学習（Deep RL）への制御パラダイムの移行、および遊脚期（Flight Phase）を含むハイブリッドダイナミクスの習得によって可能となった 4。

本稿では、この技術的跳躍を「静的安定性への拘束」から「動的効率性の追求」へのパラダイムシフトと定義し、ボディメカニクス、アクチュエータ技術、制御アルゴリズムの三つの観点からその全容を解明する。

## ---

**2\. 2023年パラダイムの技術的解剖：膝支点と受動的足首の力学**

### **2.1 「膝支点（Knee-Pivot）」歩行の数理的背景**

2023年頃までに主流であったヒューマノイドの歩行スタイルが「膝支点」と形容される現象は、制御理論上の制約に起因する必然的な形態であった。当時の多くの制御系は、ロボットの重心（Center of Mass: CoM）の挙動を予測するために、線形倒立振子モード（LIPM）を採用していた。

LIPMは、計算負荷を低減しリアルタイム制御を可能にするために、CoMの高さを一定に保つという強力な拘束条件を課す 1。CoM高さを一定に保つことで、運動方程式は線形化され、ゼロモーメントポイント（ZMP）の計算が解析的に解けるようになる。しかし、物理的な脚を持つロボットがCoM高さを一定に保ちながら歩行するためには、立脚期（スタンスフェーズ）の中盤においても膝を屈曲させた状態を維持しなければならない。人間のように膝を伸ばすと、脚長が変化しCoMが上下動してしまい、LIPMの前提条件が崩れるからである 2。

さらに、「特異点（Singularity）」の回避という幾何学的要請も、膝を曲げ続ける理由の一つであった。膝関節が完全伸展（0度）すると、脚のヤコビアン行列がランク落ちし、逆運動学（Inverse Kinematics: IK）の解が不安定になる、あるいは特定の方向への操作性が失われる問題が発生する 6。これを防ぐため、制御系は意図的に膝の可動域を屈曲側に制限し、常に「中腰」のような姿勢（Groucho walkと通称される）で運用することを余儀なくされた。この結果、外見上は膝が運動の主軸（支点）であるかのように振る舞い、大腿四頭筋に相当するアクチュエータには常に自重を支えるための巨大な保持トルクが発生し、エネルギー効率（Cost of Transport: CoT）を著しく悪化させていた 8。

### **2.2 「足首なし（Ankle-Agnostic）」の実態と機能的限界**

「足首なし」という表現は、ハードウェアとしての関節の欠如ではなく、その機能的役割の欠如を指していると解釈すべきである。2023年世代のロボットの多くは、足首関節を装備していたが、その役割は「推進」ではなく「姿勢制御」に限定されていた。

当時の制御戦略において、足首は主にZMPを支持多角形（Support Polygon）内に維持するための微調整を行うために使用された 3。地面の微小な凹凸に合わせて足裏の接地を保つ「コンプライアンス制御」や、上体の傾きを補正するためのトルク発生源としては機能していたが、人間が行うような「底屈（Plantarflexion）」による強力な蹴り出し（プッシュオフ）は行われていなかった。

これは、足部が「剛体プレート（Flat foot）」としてモデル化されており、つま先（Toe）の自由度が欠如していたことにも起因する 10。つま先関節がない場合、足首を大きく底屈させると支持多角形が極端に縮小し（点接触または線接触になる）、転倒リスクが増大する。そのため、足裏全体を地面に平行に保つような制御が支配的となり、結果として足首の能動的な運動は見かけ上排除され、ロボットは足を「置く」だけの動作（ペタペタ歩き）に終始することとなった。

## ---

**3\. 2025年パラダイムの革新：骨盤支点と全身協調運動**

2025年に向けた技術進化の核心は、ロボットの身体構造を人間のバイオメカニクスに近づけ、エネルギー効率と運動性能を最大化することにある。ここでは、「骨盤支点」への移行と「足首・股関節」の能動的活用について詳述する。

### **3.1 骨盤支点（Pelvis-Pivot）のメカニズムと3自由度ウエスト**

「骨盤支点」への移行は、腰部（Waist）および骨盤（Pelvis）の機構的自由度の拡張と密接に関連している。2023年モデルでは固定または1自由度（Yawのみ）であった腰部は、Unitree G1やTesla Optimus Gen 2において、3自由度（Yaw, Roll, Pitch）を持つ複雑なリンク機構へと進化した 12。

#### **3.1.1 骨盤回旋（Pelvic Rotation）によるストライドの拡張**

骨盤を回旋（Yaw軸回転）させる能力は、歩幅（ストライド）の決定的な増大をもたらす。人間は歩行時、遊脚側の骨盤を前方に回旋させることで、股関節の実質的な可動域を拡張している。研究によれば、骨盤回旋を導入することで、脚の長さを変えずに最大歩幅を約22%増大させることが可能である 14。  
2025年のロボットは、この骨盤回旋をアクティブに制御に取り入れている。これにより、脚を振り出す際に股関節（Hip Flexion）のみに依存する必要がなくなり、脚部アクチュエータの負荷を軽減しつつ、高速歩行を実現している。制御的には、骨盤の回旋によって生じるヨー軸周りの角運動量を、上半身や腕の逆位相の振り（Arm Swing）で相殺する全身協調制御が実装されており、これが「骨盤が運動の起点となっている」ような動作を生み出している 14。

#### **3.1.2 骨盤傾斜（Pelvic List）による重心安定化**

骨盤のロール軸運動（Pelvic List）は、遊脚側の骨盤を下げる動きを指す。これにより、立脚側の股関節を支点としてCoMの上下動を最小限に抑えることができる 16。従来のLIPMベースの「膝を曲げて高さを一定にする」アプローチとは異なり、2025年のアプローチは「骨盤を傾けることで、膝を伸ばしたままCoMの上下動を抑制する」という、より人間に近い省エネルギー戦略を採用している 18。

### **3.2 足首・股関節の能動的駆動と「直立歩行」の実現**

2025年モデルでは、足首と股関節が推進力の主要な発生源として再定義された。

#### **3.2.1 トゥ・オフ（Toe-Off）と足首のインピーダンス制御**

最新のヒューマノイド（Tesla Optimus等）は、足部に「関節化されたつま先（Articulated Toe）」を導入している 11。これは完全なアクティブ駆動（モーター入り）ではない場合もあるが、バネ機構を用いた受動的コンプライアンスを持たせることで、踵接地（Heel-strike）からつま先離地（Toe-off）への重心移動（Roll-over）を可能にしている。  
足首関節は、従来の単なる位置制御から、硬さ（Stiffness）を動的に変化させるインピーダンス制御へと移行した。これにより、着地時には衝撃を吸収し（バネとして機能）、離地時には蓄えたエネルギーを解放して前方への推進力を生み出す、アキレス腱のような機能を模倣している 9。この「スナップ」動作こそが、「足首あり」と認識される躍動感のある歩行の正体である。

#### **3.2.2 膝伸展（Straight-Leg）の許容**

制御技術の進歩（後述の強化学習など）により、特異点付近での制御安定性が向上した結果、ロボットは膝を完全に伸ばした状態（ロック状態）で体重を支えることが可能になった 2。骨格構造で荷重を受け止めることで、膝関節モーターの消費電力は劇的に低下する。これを実現するためには、膝関節アクチュエータが高いバックドライバビリティ（逆駆動性）と耐衝撃性を持ち、着地の衝撃を機械的に逃がせる構造が必要となる。

## ---

**4\. アクチュエータ技術の革新：ボディメカニクスを支えるハードウェア**

「膝支点」から「骨盤支点」への移行は、ソフトウェアだけでなく、それを物理的に駆動するアクチュエータの進化なしには語れない。

### **4.1 プラネタリーローラースクリュー（PRS）による直動駆動**

Tesla Optimusの股関節や膝関節に採用されているのが、プラネタリーローラースクリュー（Planetary Roller Screw: PRS）を用いた直動アクチュエータである 20。

* **高推力密度:** PRSは、従来のボールねじと比較して、ねじ山とローラーの接触面積が大きく、同じ体積で圧倒的に大きな荷重（数トンクラス）に耐えることができる 23。  
* **「筋肉」の模倣:** PRSアクチュエータは、回転運動を直線運動に変換するため、生物の筋肉の収縮挙動に構造的に類似している。これを大腿部や骨盤内部に配置し、リンク機構を介して関節を駆動することで、高トルクを発生させながらも脚の末端重量（イナーシャ）を最小化することに成功している。これが、高速な脚の振り出しと、強力な抗重力保持（スクワット姿勢からの立ち上がりなど）の両立を可能にした 25。  
* **インバーテッド（逆）構造:** 特にTeslaは、ナット側を回転させてスクリューロッドを進退させる「インバーテッドPRS」を採用し、省スペース化とモーター配置の最適化を図っている 26。

### **4.2 高トルク密度モーターと準直結駆動（QDD）**

一方、Unitree G1/H1などのモデルでは、高トルク密度のパンケーキ型モーターと低減速比のギアを組み合わせた「準直結駆動（Quasi-Direct Drive: QDD）」が主流である 13。

* **バックドライバビリティ:** 減速比を低く抑えることで、外部からの力に対してモーターが受動的に回転できる（バックドライバビリティが高い）。これにより、着地時の衝撃をギアボックスでなく電磁的に吸収・制御することが可能となり、追加のトルクセンサーなしでも地面の反力を推定（固有受容感覚：Proprioception）できる 28。  
* **爆発的瞬発力:** QDDは応答性が極めて高く、足首のスナップや膝の急速な屈伸など、高周波数の動作に適している。これがUnitree製ロボットの驚異的な跳躍や宙返り、そして「足首を使った」ダイナミックな走行を支えている 30。

## ---

**5\. 制御技術の進化：モデルベースから学習ベースへ**

2023年から2025年の間に起きた最大の変革は、制御アルゴリズムが解析的な「モデル予測制御（MPC）」から、データ駆動型の「強化学習（RL）」へと完全にシフトした点にある。

### **5.1 解析的MPCの限界（2023年）**

2023年以前の主流であったMPCは、LIPMなどの簡易モデルを用いて数ステップ先の未来を予測し、最適な足の着地点と反力を計算していた 31。

* **限界:** MPCはモデルの不確実性に弱く、また「膝を伸ばす」ような非線形性の強い領域での計算が困難であった。さらに、地面との接触タイミングのズレ（着地衝撃）に対して保守的なゲイン設定を必要とし、結果としてロボットは「慎重に、膝を曲げて」歩かざるを得なかった 4。

### **5.2 エンドツーエンド強化学習の台頭（2025年）**

2025年のロボット（Unitree、Tesla、Figure AI等）は、物理シミュレータ（NVIDIA Isaac Sim, MuJoCo等）上で何十億歩もの試行錯誤を経たニューラルネットワークによって制御されている 5。

* **創発的な歩行:** 強化学習において、エンジニアは「膝を曲げろ」とはプログラムしない。代わりに「最小のエネルギーで、転倒せずに、目的地へ移動せよ」という報酬関数を与える。すると、シミュレーション上のロボットは、物理法則（重力、摩擦、慣性）を利用して、エネルギー効率の良い「膝を伸ばした歩行」や「骨盤の回旋」、「腕の振り」を自ら発見（創発）する 5。  
* **ドメインランダム化（Domain Randomization）:** シミュレーションと現実のギャップ（Sim-to-Real gap）を埋めるため、摩擦係数、ロボットの質量、センサーノイズなどをランダムに変動させて学習させる。これにより、未知の路面や外乱に対しても、事前のプログラミングなしで即座にバランスを保つロバスト性を獲得した 33。Unitree G1が「わずか2日で人間のような歩行を学習した」という報告は、この技術の成熟を象徴している 35。

### **5.3 ハイブリッドダイナミクスと遊脚期（Flight Phase）の制御**

走る（Running）動作には、両足が地面から離れる「遊脚期（Flight Phase）」が存在する。これはLIPMでは記述できない状態であるが、2025年の制御系は「バネ負荷倒立振子（Spring-Loaded Inverted Pendulum: SLIP）」モデルの概念を強化学習に組み込むことでこれを克服した 36。

* **制御リャプノフ関数（CLF）:** 着地と離地という不連続な切り替え（ハイブリッドシステム）において、安定性を数学的に保証するために、制御リャプノフ関数を学習のガイドとして用いる手法が実用化されている 38。これにより、ロボットはジャンプや走行といった、足首のバネ性を最大限に活かすダイナミックな動作を安定して行えるようになった。

## ---

**6\. 比較技術要約**

以下の表は、2023年と2025年の技術的特徴を対比させたものである。

| 特徴 | 2023年パラダイム（膝支点/足首なし） | 2025年パラダイム（骨盤支点/足首・股関節あり） |
| :---- | :---- | :---- |
| **主要な運動支点** | 膝関節（常時屈曲） | 股関節・骨盤（伸展・回旋） |
| **腰部（Waist）自由度** | 剛体または1自由度（Yawのみ、固定運用多し） | **3自由度（Yaw, Roll, Pitch）能動制御** 12 |
| **歩行スタイル** | "Groucho Walk"（中腰、すり足） | "Straight-Leg"（膝伸展、踵接地〜つま先離地） |
| **ストライド生成機序** | 股関節の屈曲のみによる脚の振り出し | \*\*骨盤回旋（Pelvic Rotation）\*\*による脚リーチの拡張 14 |
| **足首の役割** | 受動的安定化（ZMP維持、接地補正） | **能動的推進（プッシュオフ）**、衝撃吸収、エネルギー回生 |
| **つま先（Toe）機構** | 剛体プレート（機能なし） | **関節化（Articulated）**、バネ復帰または能動駆動 11 |
| **主要アクチュエータ** | 高減速比ギア / ボールねじ | **プラネタリーローラースクリュー（PRS）** / QDDモーター 22 |
| **制御理論** | 解析的MPC（LIPMベース、静的安定重視） | **深層強化学習（End-to-End RL）**、SLIPモデル（動的安定重視） |
| **エネルギー効率** | 低（自重保持に常時トルクが必要） | 高（骨格支持と受動的ダイナミクスの活用） |
| **移動速度** | \< 1.5 m/s（早歩き程度） | \> 3.0 m/s（空中局面を伴う走行） 27 |

## ---

**7\. 深層考察と結論：ロボットはなぜ「ヒト」に近づいたのか**

本調査から得られた洞察は、2023年から2025年の進化が単なるスペックの向上ではなく、ロボット工学が「制御しやすい形」から「生物として効率的な形」へと回帰したプロセスであるという点である。

1. **ハードウェアとソフトウェアの共進化:** 「直立歩行」は、着地時の激しい衝撃（Impact）を伴う。これに耐えうる**プラネタリーローラースクリュー**や**バックドライバビリティの高いQDDアクチュエータ**というハードウェアの進化がなければ、強化学習が導き出すアグレッシブな歩行ポリシーはロボットを破壊していただろう。逆に、強化学習という強力なソルバーがなければ、多自由度の骨盤や非線形な直動アクチュエータを協調させて制御することは不可能に近かった 30。  
2. **骨盤の「再発見」:** 2023年のロボット工学において無視されがちであった「骨盤」は、エネルギー効率の乗数として再評価された。骨盤を回旋させることで、脚の慣性モーメントを見かけ上減らし、同じパワーでより速い脚の切り替えが可能となった。さらに、3自由度の腰部は、脚の激しい動きが生成する角運動量を上半身で吸収する「ダンパー」としての役割も果たしている 14。  
3. **歩行のコモディティ化:** Unitree G1が短期間で高度な歩行を獲得した事実は、二足歩行という課題そのものが、もはや解決困難な難問ではなく、適切なハードウェアと学習環境があれば再現可能な「コモディティ技術」になりつつあることを示唆している 35。

結論として、2025年のヒューマノイドロボットは、骨盤を運動の要（支点）とし、足首と股関節をバネのように使うことで、人間と同等あるいはそれ以上の移動効率と運動性能を獲得しつつある。これは、ユーザーの提示した「2023年は膝支点、2025年は骨盤支点」という仮説を、力学的および制御工学的観点から完全に裏付けるものである。

#### **引用文献**

1. Straight leg walking strategy for torque-controlled humanoid robots, 12月 23, 2025にアクセス、 [https://core.ac.uk/download/pdf/199225165.pdf](https://core.ac.uk/download/pdf/199225165.pdf)  
2. Straight leg walking strategy for torque-controlled humanoid robots, 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/314203705\_Straight\_leg\_walking\_strategy\_for\_torque-controlled\_humanoid\_robots](https://www.researchgate.net/publication/314203705_Straight_leg_walking_strategy_for_torque-controlled_humanoid_robots)  
3. (PDF) Human-like walking with toe supporting for humanoids, 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/221065032\_Human-like\_walking\_with\_toe\_supporting\_for\_humanoids](https://www.researchgate.net/publication/221065032_Human-like_walking_with_toe_supporting_for_humanoids)  
4. Optimization and Learning Based Control for Bipedal Robots, 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/392901582\_Optimization\_and\_Learning\_Based\_Control\_for\_Bipedal\_Robots](https://www.researchgate.net/publication/392901582_Optimization_and_Learning_Based_Control_for_Bipedal_Robots)  
5. Gait-Conditioned Reinforcement Learning with Multi-Phase ... \- arXiv, 12月 23, 2025にアクセス、 [https://arxiv.org/html/2505.20619v1](https://arxiv.org/html/2505.20619v1)  
6. Tesla Optimus walking : r/robotics \- Reddit, 12月 23, 2025にアクセス、 [https://www.reddit.com/r/robotics/comments/1afrvs0/tesla\_optimus\_walking/](https://www.reddit.com/r/robotics/comments/1afrvs0/tesla_optimus_walking/)  
7. Exploiting Arch-like Foot Structure for Knee-Extended Walking in ..., 12月 23, 2025にアクセス、 [https://www.mdpi.com/2313-7673/10/2/96](https://www.mdpi.com/2313-7673/10/2/96)  
8. Development of Humanoid Walking Robot with a Bio-inspired Knee ..., 12月 23, 2025にアクセス、 [https://research.manchester.ac.uk/en/studentTheses/development-of-humanoid-walking-robot-with-a-bio-inspired-knee-jo/](https://research.manchester.ac.uk/en/studentTheses/development-of-humanoid-walking-robot-with-a-bio-inspired-knee-jo/)  
9. Passive knee flexion increases forward impulse of the trailing leg ..., 12月 23, 2025にアクセス、 [https://arxiv.org/html/2411.13289v1](https://arxiv.org/html/2411.13289v1)  
10. Study of Toe Joints to Enhance Locomotion of Humanoid Robots, 12月 23, 2025にアクセス、 [https://crlab.cs.columbia.edu/humanoids\_2018\_proceedings/media/files/0186.pdf](https://crlab.cs.columbia.edu/humanoids_2018_proceedings/media/files/0186.pdf)  
11. Stepping Forward: The Debate Over Active vs. Passive Toes in ..., 12月 23, 2025にアクセス、 [https://www.humanoidsdaily.com/news/stepping-forward-the-debate-over-active-vs-passive-toes-in-humanoid-robotics](https://www.humanoidsdaily.com/news/stepping-forward-the-debate-over-active-vs-passive-toes-in-humanoid-robotics)  
12. 12月 23, 2025にアクセス、 [https://www.emergentmind.com/topics/unitree-g1-humanoid\#:\~:text=The%20Unitree%20G1%20features%20a,both%20power%20and%20precision%20grasps.](https://www.emergentmind.com/topics/unitree-g1-humanoid#:~:text=The%20Unitree%20G1%20features%20a,both%20power%20and%20precision%20grasps.)  
13. Unitree G1 Humanoid Robot by Unitree Robotics \- Top 3D Shop, 12月 23, 2025にアクセス、 [https://top3dshop.com/product/unitree-robotics-g1-humanoid-robot](https://top3dshop.com/product/unitree-robotics-g1-humanoid-robot)  
14. Whole-Body Walking Pattern Using Pelvis-Rotation for Long Stride ..., 12月 23, 2025にアクセス、 [http://dyros.snu.ac.kr/wp-content/uploads/2021/08/0095.pdf](http://dyros.snu.ac.kr/wp-content/uploads/2021/08/0095.pdf)  
15. Optimization of Pelvic Rotation Walking Pattern Considering Future ..., 12月 23, 2025にアクセス、 [https://ieeexplore.ieee.org/iel7/6287639/9668973/09762273.pdf](https://ieeexplore.ieee.org/iel7/6287639/9668973/09762273.pdf)  
16. Dynamic Principles of Gait and Their Clinical Implications \- PMC, 12月 23, 2025にアクセス、 [https://pmc.ncbi.nlm.nih.gov/articles/PMC2816028/](https://pmc.ncbi.nlm.nih.gov/articles/PMC2816028/)  
17. A Preliminary Study into the Effects of Pelvic Rotations on Upper ..., 12月 23, 2025にアクセス、 [https://www.researchgate.net/profile/Andrew-Pennycott/publication/258255215\_A\_preliminary\_study\_into\_the\_effects\_of\_pelvic\_rotations\_on\_upper\_body\_lateral\_translation/links/00b7d526ec66dc09e1000000/A-preliminary-study-into-the-effects-of-pelvic-rotations-on-upper-body-lateral-translation.pdf](https://www.researchgate.net/profile/Andrew-Pennycott/publication/258255215_A_preliminary_study_into_the_effects_of_pelvic_rotations_on_upper_body_lateral_translation/links/00b7d526ec66dc09e1000000/A-preliminary-study-into-the-effects-of-pelvic-rotations-on-upper-body-lateral-translation.pdf)  
18. Utilization of Human-Like Pelvic Rotation for Running Robot \- Frontiers, 12月 23, 2025にアクセス、 [https://www.frontiersin.org/journals/robotics-and-ai/articles/10.3389/frobt.2015.00017/full](https://www.frontiersin.org/journals/robotics-and-ai/articles/10.3389/frobt.2015.00017/full)  
19. Leg and lower limb dynamic joint stiffness during different walking ..., 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/344411995\_Leg\_and\_lower\_limb\_dynamic\_joint\_stiffness\_during\_different\_walking\_speeds\_in\_healthy\_adults](https://www.researchgate.net/publication/344411995_Leg_and_lower_limb_dynamic_joint_stiffness_during_different_walking_speeds_in_healthy_adults)  
20. New Breakthrough of Tesla's Humanoid Robot Bionic Knee Joint ..., 12月 23, 2025にアクセス、 [https://acgrobot.com/new-breakthrough-of-teslas-humanoid-robot-bionic-knee-joint-patent-revealed-single-motor-drives-150-degree-large-range-movement/](https://acgrobot.com/new-breakthrough-of-teslas-humanoid-robot-bionic-knee-joint-patent-revealed-single-motor-drives-150-degree-large-range-movement/)  
21. Tesla Bot's Leg Actuator Lifting A Half-Ton Piano : r/teslamotors, 12月 23, 2025にアクセス、 [https://www.reddit.com/r/teslamotors/comments/yg3l0j/tesla\_bots\_leg\_actuator\_lifting\_a\_halfton\_piano/](https://www.reddit.com/r/teslamotors/comments/yg3l0j/tesla_bots_leg_actuator_lifting_a_halfton_piano/)  
22. Humanoid robots 101 \- Bank of America Institute, 12月 23, 2025にアクセス、 [https://institute.bankofamerica.com/content/dam/transformation/humanoid-robots.pdf](https://institute.bankofamerica.com/content/dam/transformation/humanoid-robots.pdf)  
23. Planetary Roller Screws: Essential for Robotics \- Holistic News, 12月 23, 2025にアクセス、 [https://holistic.news/en/planetary-roller-screws-essential-for-robotics/](https://holistic.news/en/planetary-roller-screws-essential-for-robotics/)  
24. Why use a Satellite Roller Screw? | News \- ABSSAC Ltd, 12月 23, 2025にアクセス、 [https://www.abssac.co.uk/n/+Why+use+a+Satellite+Roller+Screw/254/](https://www.abssac.co.uk/n/+Why+use+a+Satellite+Roller+Screw/254/)  
25. From sci-fi to factory floors: Humanoid robots at the crossroads of ..., 12月 23, 2025にアクセス、 [https://kr-asia.com/from-sci-fi-to-factory-floors-humanoid-robots-at-the-crossroads-of-technology-capital-and-ambition](https://kr-asia.com/from-sci-fi-to-factory-floors-humanoid-robots-at-the-crossroads-of-technology-capital-and-ambition)  
26. An article sorting out the typical representative companies of ..., 12月 23, 2025にアクセス、 [https://en.eeworld.com.cn/mp/ICVIS/a393932.jspx](https://en.eeworld.com.cn/mp/ICVIS/a393932.jspx)  
27. Intelligent Mobility: How Unitree's Robots Move in the Real World, 12月 23, 2025にアクセス、 [https://robotvillage.com/blogs/f/intelligent-mobility-how-unitree%E2%80%99s-robots-move-in-the-real-world](https://robotvillage.com/blogs/f/intelligent-mobility-how-unitree%E2%80%99s-robots-move-in-the-real-world)  
28. (PDF) Exploring Challenges and Opportunities in Manufacturing and ..., 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/395094461\_Exploring\_Challenges\_and\_Opportunities\_in\_Manufacturing\_and\_Intelligence\_for\_Future\_Robotics](https://www.researchgate.net/publication/395094461_Exploring_Challenges_and_Opportunities_in_Manufacturing_and_Intelligence_for_Future_Robotics)  
29. Humanoid Robot HRP-5P: an Electrically Actuated ... \- ResearchGate, 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/330743210\_Humanoid\_Robot\_HRP-5P\_an\_Electrically\_Actuated\_Humanoid\_Robot\_with\_High\_Power\_and\_Wide\_Range\_Joints](https://www.researchgate.net/publication/330743210_Humanoid_Robot_HRP-5P_an_Electrically_Actuated_Humanoid_Robot_with_High_Power_and_Wide_Range_Joints)  
30. A Variable Reduction Ratio Design Paradigm for Humanoid Robots ..., 12月 23, 2025にアクセス、 [https://arxiv.org/html/2506.12314v1](https://arxiv.org/html/2506.12314v1)  
31. Walking control of humanoid robots based on improved footstep ..., 12月 23, 2025にアクセス、 [https://pmc.ncbi.nlm.nih.gov/articles/PMC11885507/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11885507/)  
32. Global Position Control on Underactuated Bipedal Robots: Step-to ..., 12月 23, 2025にアクセス、 [http://ames.caltech.edu/xiong2021global.pdf](http://ames.caltech.edu/xiong2021global.pdf)  
33. Simulated humanoid robots learn to hike rugged terrain autonomously, 12月 23, 2025にアクセス、 [https://news.engin.umich.edu/2025/09/simulated-humanoid-robots-learn-to-hike-rugged-terrain-autonomously/](https://news.engin.umich.edu/2025/09/simulated-humanoid-robots-learn-to-hike-rugged-terrain-autonomously/)  
34. Humanoid Unveils Record Breaking Bipedal Robot Walking 48 ..., 12月 23, 2025にアクセス、 [https://humanoidroboticstechnology.com/industry-news/humanoid-unveils-record-breaking-bipedal-robot-walking-48-hours-after-assembly/](https://humanoidroboticstechnology.com/industry-news/humanoid-unveils-record-breaking-bipedal-robot-walking-48-hours-after-assembly/)  
35. Too Fast? Unitree G1 Achieves Human-like Walking Gait in 2 Days ..., 12月 23, 2025にアクセス、 [https://www.youtube.com/watch?v=WG0fc7W8o3g](https://www.youtube.com/watch?v=WG0fc7W8o3g)  
36. Humanoid Running: Adaptive SLIP Trajectories \- Emergent Mind, 12月 23, 2025にアクセス、 [https://www.emergentmind.com/papers/2512.13304](https://www.emergentmind.com/papers/2512.13304)  
37. Humanoid Robot Running Through Random Stepping Stones and ..., 12月 23, 2025にアクセス、 [https://arxiv.org/html/2512.13304v1](https://arxiv.org/html/2512.13304v1)  
38. Chasing Stability: Humanoid Running via Control Lyapunov ... \- arXiv, 12月 23, 2025にアクセス、 [https://arxiv.org/html/2509.19573v1](https://arxiv.org/html/2509.19573v1)  
39. (PDF) Chasing Stability: Humanoid Running via Control Lyapunov ..., 12月 23, 2025にアクセス、 [https://www.researchgate.net/publication/395807043\_Chasing\_Stability\_Humanoid\_Running\_via\_Control\_Lyapunov\_Function\_Guided\_Reinforcement\_Learning](https://www.researchgate.net/publication/395807043_Chasing_Stability_Humanoid_Running_via_Control_Lyapunov_Function_Guided_Reinforcement_Learning)  
40. Optimization of Pelvic Rotation Walking Pattern Considering Future ..., 12月 23, 2025にアクセス、 [http://dyros.snu.ac.kr/wp-content/uploads/2022/05/ParkBY\_Optimization-of-Pelvic-Rotation-Walking.pdf](http://dyros.snu.ac.kr/wp-content/uploads/2022/05/ParkBY_Optimization-of-Pelvic-Rotation-Walking.pdf)
