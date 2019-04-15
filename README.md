# 数独スパコン物語

## はじめに

2013年3月8日金曜日、arXivに一編の論文が掲載された。

[arXiv:1303.1886](https://arxiv.org/abs/1303.1886)

タイトルは「レプリカ交換モンテカルロで作成された数独の難問 (Difficult Sudoku Puzzles Created by Replica Exchange Monte Carlo Method)」。投稿者は、当時東京大学物性研究所に務めていた研究者、すなわち私であった。私はまだこの論文が人生を(悪い意味で)大きく変えることになろうととは想像だにしていなかった。

研究者は論文を書くと、それを査読論文誌に投稿する。そして、査読を経て、出版が許可され、その論文誌から出版されると、その論文が研究者の「業績」となる。しかし、分野にもよるが、査読には一般に数週間から数ヶ月ほどかかるため、研究者は事前にarXivと呼ばれるプレプリントサーバに論文をアップロードすることが多い。似たようなことを誰かがやっている時、自分の方が早く論文を書いたのに競争相手の査読が先に終わって先に出版されては困るので、とりあえずやったことを公開する、という動機もあるし、論文を投稿する前にプレプリントサーバにアップロードし、広く研究者の意見を聞いてから投稿先を決める、といった目的もある。先の論文は後者の意図でアップロードされた。

プレプリントサーバには、毎日世界中から多くの論文が投稿される。研究者は、自分に近い分野についてそれをチェックする。朝のプレプリントサーバのチェックから一日を始める研究者もいる。先の「数独論文」は、そんな研究者に発見され、Twitterで「数独の難問をスパコンで作成。東大物性研の研究者」といった内容で紹介された。これが著名な物理学者にリツィートされることで一気に広まった。「スパコンで数独の難問を作った」というニュースは瞬く間に拡散され、研究者以外の一般の数独愛好家にも届いた。そして「スパコンで作り、人の手ではまず解けないだろう」とされた問題が、あっというまに解けてしまう問題であることがわかり、今度は「スパコンで作った数独、人間にあっというまに解かれる」という「痛いニュース」として拡散され、「ねとらぼ」などの著名なメディアにも取り上げられた。所謂「炎上」状態であった。

なぜこういうことになったのか？何がいけなかったのか？そしてその後どうなったのか？

このリポジトリは、一連の数独騒動と、その後の数独プロジェクトについての記録である。

なお、数独は株式会社ニコリの登録商標であり、一般名称としては「ナンプレ」と表記するのが良いが、筆者が「数独」という呼称に慣れているため、ここで登録商標であることを明記の上、以後は「数独」という呼び方で統一する。

## 始まりと炎上と

「なんだこりゃ？」

2010年8月のある日、いつものように出勤し、ひとしきり仕事をして、昼食のために外に出ようとした私は、研究室の扉に一枚の紙が貼ってあることに気がついた。そこには「数学のエキスパートが3ヶ月かけて作成した「世界一難しい数独」というタイトルとともに、一つの数独の問題が描いてあった。当時同室だったTさんが貼ったものに違いない。しかも、部屋の「内側」に貼ってあったということは、これは私に向けた「解いてみろ」とのメッセージだ。

数独を自分の手で解いたことはほとんど無いが、プログラムで解いたことならあった。高校生の頃だったか、大学一年生の頃だったか、私は突然、情報処理技術者試験を受けようと思い立った。なぜそう思ったかは覚えていないが、とりあえず手始めにと書店にあった「合格情報処理」という雑誌を買って勉強した。その雑誌のコーナーの一つとして「数独」があった。私は勉強の合間に数独を解くプログラムを書いてみたのだった。おそらくこれが私と数独との初めての出会いであったと思う。要するに手で解くより先に、プログラムで解いたのだった。

「数学のエキスパートが作成した難問」を見た時も、手で解こうとは全く思わず、とりあえずRubyで解法プログラムを組んだ。実行すると答えはすぐに出た。おそらく10ミリ秒もかかっていない。私はその答えを問題の隣に貼り出し、満足して昼食にでかけたのだった。

そんなことがあったことなどしばらく忘れていたのだが、2013年になって、私が所属するプロジェクトで「レプリカ交換モンテカルロ法」の効率が話題となった。詳細は後述するが、これは東大の福島さんが考案した手法で、普通のモンテカルロ法に比べて非常に効率がよくなる手法だ。特にスパコンでの並列計算と相性が良いことから、HPC(High Performance Computing)分野で広く使われていた。しかし、これがどういう問題で、どういう時に効率がよくなるのか、当時の私はよくわかっていなかった。その時、突然3年前の「世界一難しい数独」のことを思い出した。

「スパコンでレプリカ交換モンテカルロ法を使えば、世界一難しい数独を作ることができるんじゃないだろうか？」

2013年当時、私は「京コンピュータ」の全ノード計算をするなど、それなりに大規模な計算を行っていた。それに伴い、気持ちも大きくなっていたのだろう。世界有数の計算機を使えるのだから、世界有数の「なにか」ができるに違いない。そんな考えが傲慢であったことを、私は後で痛いほど思い知ることになる。

私は数独の難易度を定義し、スパコンで難問を探索させた。そして、適当にやった場合は全く難問が見つからず、交換モンテカルロ法を用いた場合には効率よく難問が見つかることを確認した。満足した私は、その手法と結果を論文にまとめ、スパコンが見つけて来た「最も難しい問題」に「人間にはまず解けないだろう」という刺激的な説明をつけて、プレプリントサーバに投稿したのだった。

その後の騒動は前述の通りである。それまでもプチ炎上はあったが、ここまでの炎上は初めての経験である。よせば良いのにエゴサーチをして「評判」を調べた。私は「自分は心が強いほうだ」と思っているが、「アホか」「自分で解いてみなかったのか？」「東大暇そうだな」などのコメントが並ぶのを見て心が折れ、その日は職場を早退したのだった。

## 数独プロジェクト立ち上げ

家に帰り、嫁さんに心配されたが、一晩寝たらメンタルはほぼ回復した。繰り返しになるが、私は心が強い方だ。次の日から、自分の失敗原因を検討していた。いろいろまずかった点はあるが、最もまずかったのは「一人だった」ことだと結論した。仲間が必要だ。一緒に「世界一」を目指し、そしてなにか失敗シた時に「一緒に恥をかいてくれる」仲間が。

その時、僕の頭には一人の人物が浮かんでいた。未踏ユースプロジェクトで同期だったKさんだ。KさんはOSを自作するというテーマで未踏に採択された。未踏のブースト会議のプレゼンで、(GCCの吐いたコードではなく)GCCを改造するという話をしたのを明瞭に覚えていた。彼はその後会社に就職していたが、会社のブログで数独のプログラムを書いているのを見たことがあった。彼なら一緒にプロジェクトをやってくれるかもしれない。

私はエゴサーチしていた時に、私の論文に反応していたNさんに連絡をとった。Nさんも未踏の同期で、Kさんと同じ会社に勤めていた。こうしてNさんの紹介により、私はKさんを始めとする多くの人に出会い、「数独難問作成プロジェクト」を立ち上げることになった。

「炎上」から4ヶ月ほどたった2013年7月2日、私は私は某社にいた。そこには未踏の同期のNさん、Kさん、後に未踏PMになるTさん他、数名の方がいた。未踏ユースが2002年度なので、最後にKさんにお会いしたのは10年以上前のことになる(Nさんには名古屋大学での研究会や、竹内先生の最終講義でお会いしたことがあった)。そこで、簡単に自分の状況を説明した。数独の難易度の定義を間違えて「炎上」したこと、その後反省して世の中にある数独の解法をあらかた実装したこと、一緒にやってくれる仲間が欲しいこと、某社には50%ルールというものがあるということを聞いたこと、etc。

とりあえずその時に、Cybozu Liveというグループ掲示板を開設し、そこでプロジェクトの進捗を報告することになった。掲示板が立ち上がった次の日の朝、「こんな最適化はどうか？」というアイディアが出て、実装してみたら速度が4倍になった。当時の私の日記に驚きが記されている。

> 昨日協力を依頼して、今朝でたアイディアを実装したらコードが4倍高速化された。 あの会社、こういう人々をたくさん抱えているのか・・・
> いやはや。

この掲示板から様々なアイディアが生まれ、学びがあったが、とてもその全てを書ききることはできない。まだ頭の中がまとまっていないが、アトランダムに思いついたことを書いていく。

## 数独の難易度とは？

私が最初に定義した「難易度」には問題があり、計算機が解くには苦労するが、人間にはあっさり解けてしまったことはすでに述べた。ここで私が定義した「難易度」について述べておきたい。

まず、数独のルールと用語を定義しておこう。数独は9x9に区切られたマスに、予めおいてあるヒント数字をもとに、残りのマスを埋めていくペンシルパズルである。9x9のマスはさらに3x3に分けられており、それぞれを「ボックス」と呼ぶことにしよう。数独は「行」「列」「ボックス」それぞれに「1から9までの数字が一度だけ登場する」というルールで、全てのマスを埋めるのが目的だ。この「行」「列」「ボックス」それぞれを「ユニット」と呼ぶことにする。「一つのユニット」には、同じ数字が入ってはならない。

さて、数独の問題をプログラムで解く場合、「マス」に注目するのが一般的だと思われる。一つのマスは「行」「列」「ボックス」の３つのユニットに所属する。この所属するユニットにあるヒント数字を、「マスから見える」と呼ぼう。あるマスに入る可能性のある数字は、「マスから見えない」数字だけである。そこで、まず全てのマスについて「マスから見えない」数字のメモを取る。これを「ペンシルマーク」と呼ぶ。もし「ペンシルマーク」が一つしかなければ、このマスに入る数字は一つしかないからその数字で確定である。あるマスが確定したら、そのマスから見えるマス全てのペンシルマークからその数字を消す。簡単な問題はこの連鎖だけで解けてしまう。

しかし、難しい問題は、消せるペンシルマークを全て消しても、マスが決まらない状態になるであろう。その時は、ペンシルマークの数がもっとも少ないマスを選び、そのマスの数字を「仮置き」して再帰する。最後まで矛盾なしに埋めることができれば完成、矛盾したら「仮置き」した数字は間違いなので、別の数字を試す。こうして任意の数独を解くことができる。

TODO: 図を作る。

さて、再帰は「木」を作る。私は、この再帰木の大きさ(つまり「葉」の数)を「難易度」と定義した。多くの場合、再帰は二択のあるマスについて行う。したがって、再帰するほど盤面は倍々ゲームで増えていく。答えが再帰深さ8にある場合は、運が悪いと2の8乗、つまり256回再帰しないと答えにたどり着けない。平均的にも100回以上再帰が必要であろう。私が作った問題は、答えが再帰レベル10にあり、かつ答え以外の枝の再帰レベル深さが10以上あったため、再帰木の大きさは10万を超えた。平均的に5万再帰が必要になる問題であり、ちょっと人間にはどうにもならない領域であることが想像できるであろう。

「なぜ自分で解いて見なかったのか」

後によく問われる言葉であるが、答えが「再帰レベル10」にあり、平均5万再帰必要な問題とわかっている問題を「手で解こう」という気にはならなかった。

さて、なぜ「平均5万再帰」も必要であるはずの問題が、あっという間に人間に解かれてしまったのか。それは「計算機の解き方」と「人間の解き方」が異なるからだ。計算機は「マス」に注目して解いたが、人間は「ユニット」、特に「ボックス」に注目して問題を解く。

TODO: 人間が解く方法の説明の図

先程作られた問題を見ると、ある行に着目すると、そこである数字が入るのは一つに決まってしまう。その数字を決めると、別の数字も、さらに別の数字も、とガタガタとマスが決まってしまい、問題は解けてしまう。

これは「問題の難易度は、採用する解法に強く依存する」ということを意味する。たとえ、ある指標で「難問」と定義されても、その後で新しく開発された「解法」により、その問題が簡単に解けてしまう可能性を否定できないのだ。この、「ある難易度が、その後開発された手法により適切でなくなってしまう」現象は、プロジェクト中に実際に起きることになる。

もう一つ、「人間の考え方」と「計算機の考え方」は違う、ということも重要な教訓であった。人間は計算機に比べて「情報のコピー」に対するペナルティが大きい。つまり、再帰する時も計算機のように状態をまるごとコピーしたりせず、状態を書いたり消したりと、部分アップデートをする。この知見は私が後で「計算機の知能」を考える上で重要なファクターとなった。

## 数独進捗報告にプロが紛れ込む

物性研究所では物理学会の報告会というものを開催していた。日本の物理学者の多くは日本物理学会に所属しており、年に二度の「学会」で自分の研究を報告する。よくマンガで「異端の説を唱えて学会を追放された研究者」とか「私は学会に復讐してやるんだ」と叫ぶマッドサイエンティストが出てきたりして、「学会」はまるで権威の象徴のように描かれることが多いが、実際に学会は参加費さえ払えば誰でも審査なしで発表できる場である。そのため、異端を排除するどころか、アマチュア研究者がトンデモな説を学会で披露することも珍しくない。

話がそれた。

ともかく、物理学者は年に二度、「学会」で発表を行うのが常だが、学会の直後に同じ内容を物性研で「再放送」することで、お互いが何をやっているか把握、交流しましょう、というのが物性研の「学会発表講演会」である。私は無論、学会では真面目な発表をしていたが、この場を借りて数独プロジェクトの進捗報告を行うことにした。

さて、講演会その日、見慣れない参加者が二名いた。内部の発表会だが、特にクローズドな会というわけでも無いので、誰でも参加できた。自分の番が来て、数独の発表をしたら、その人からなんかやたらと詳しい感じで質問が来た。休み時間に聞いてみると、その方はTさんとFさんと名乗り、「出版社に勤め、数独を作る人」、すなわち「数独のプロ」であることが判明した。その方の著書である「数独」の本を何冊も頂いた。これを読んで勉強しろ、ということであろう。私が知る限り、物性研の「学会発表講演会」に物理学者でない人が参加したのは後にも先にもこの一度だけである。このTさんとはその後なんどかお会いする機会があった。また、関西で行われるパズルの会(?)に何度かお誘いいただいたのだが、本業が忙しくて参加できずじまいだったのは申し訳なかった。

## 数独イベントに呼ばれる

2016年5月30日、神保町にある書店「書泉グランデ」で、数独のイベントが行われ、私はそこに呼ばれた。正確には数独のイベントではなく、岩波データサイエンスという本の出版イベントで、イベント名も「ナンプレと魔方陣　解いたり作ったり数えたり」というタイトルであった。最初に数独世界一の方による数独のリアルタイムチャレンジのあと、私の話をした。

TODO: 続きを書く

## 一般公開で数独を配る

物性研の一般公開、及び「京」の一般公開で「スパコンが作った数独」を配った。

TODO: 続きを書く

## モンテカルロ法はなぜダメだったか？

TODO: 書く

## まとめ

TODO: 書く