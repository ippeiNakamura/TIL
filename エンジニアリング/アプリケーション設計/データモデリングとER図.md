# データモデリング
+ リソースの関係性を示すこと
+ 目的はデータベースを作成するためで、そのために「どのようなデータが必要か、関係性をどうするか」明らかにする。
+ データモデリングするには、３つの過程を取る。
1. 概念データモデル
2. 論理データモデル
3. 物理データモデル

## わからないこと
+ なぜ、商品と注文商品の２つのリソースに分けているのか？
+ 注文商品は商品が持つ状態であり、注文商品は商品の属性にならないのか？

# 概念データモデル
+ データが持つ属性は着目せず、リソース同士の関係性を明確にする。

# 論理データモデル
+ 属性を洗い出し、正規化（重複した属性を無くす作業）を行う

# 物理データモデル
+ 実際に使うDBに応じて、設計する

# ER図
+ エンティティ同士の関係性を図示したもの
+ エンティティとは、リソースの構成要素のこと。つまりデータ
+ ここでは、エンティティが持つ属性は気にせず、関係性に着目する。

# 属性を列挙する
+ エンティティの属性を洗い出す
## 私の場合
| エンティティ名 | 属性 | 目的| 
|----|---- |----|
| ユーザー | ID | ユーザーを識別する |
| | 氏名 | ユーザーの名前 |
|  | メールアドレス | 連絡用のアドレス |
| ゴール | ID|ゴールを識別するため|
|  | 内容|達成したい目標の内容|
|  | 目標期日|ゴールを達成したい目標の日付|
|  | 着地予想日|実際にゴールが達成されるであろう現実的な日付|
|  | 達成率|ゴールの達成割合|
|  | 進捗|期日と着地予想日を比較した結果「予定通り」・「予定遅れ」を返す|
|  | 進捗度合い|進捗の度合いを日で返す「〇〇日分予定遅れ」の〇〇部分|
| スモールゴール | ID|スモールゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | 目標期日|スモールゴールを達成した目標の日付|
|  | 着地予想日|実際にスモールゴールが達成されるであろう現実的な日付|
|  | 達成率|ゴールの達成割合|
|  | 進捗|期日と着地予想日を比較した結果「予定通り」・「予定遅れ」を返す|
|  | 進捗度合い|進捗の度合いを日で返す「〇〇日分予定遅れ」の〇〇部分|
| タスク | ID|タスクを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | ステータス|そのタスクの状況「完了」「未完了」を返す|
|  | 想定作業時間|タスクにかかる想定の作業時間|
| カレンダー |ID|カレンダーを識別するため|
| 予定 |ID|予定を識別するため|
|  |タスク|　予定するタスク|
|  |開始日時|予定が開始する日時|
|  |終了日時|予定が終了する日時|
| 実際 |ID|実際を識別するため|
|  |タスク|　予定するタスク|
|  |開始日時|実際が開始する日時|
|  |終了日時|実際が終了する日時|
| 成長 |ID|成長を識別するため|
|  |カテゴリー|成長の種類「学んだこと」「できるようになったこと」を返す|
|  |内容|ユーザー自身が成長したと感じることを書く|

## IDと〇〇番号を分ける理由
+ ○○番号は、業務上使用する番号のことで、IDとごっちゃにしてしまうと、例えば業務上の理由で〇〇番号を変えたいとなった場合に、識別できない可能性がでてくるため。

# 正規化
+ 「重複する属性を無くす作業」
## 第一正規化
+ 一つのデータに含まれる繰り返し項目を行分割することで、一つの行として独立させる。
+　例えば、ある注文データには、複数の商品が含まれているという状態を想定した場合、
+ この状態をエクセルの表でイメージすると、ある１行分の注文データに、「商品」列が「商品A」「商品B」のように複数行になってしまう。　
+ この一行の中に複数行出来上がっている状態を、複数行に分割する作業のことを第一正規化という。

+ またこの時点で、他のカラムから導出可能なカラムは削除すると良いらしい。
+ 例えば、「〜合計」のような、エクセルでいうSUM関数を使えば出せるような列のこと。
+ これはSUM関数で参照している列があれば算出できるため、このような列は削除する

## 私の場合
+ 正しいがわからないが、私の場合下記のように問うことでチェックしてみた。
1. ある一つの行が複数の値を持つ可能性がないか
2. ある列は、他の列から算出可能でないか
+ 1.はなかったが、2.は結構発見できて、改善された！
例えば下記の８つのレコードは不要だと判断できた！
＜ゴール＞
+ ゴールの達成率＝スモールゴールの達成率の合計/スモールゴール数
+ ゴールの進捗度合い=ゴールの期日-ゴールの着地予想日
+ ゴールの着地予想日=タスクの想定作業時間の合計を、営業日日付でならした日
+ ゴールの進捗=ゴールの進捗度合いが0以上なら、「予定通り」。そうでないなら「予定遅れ」
＜スモールゴール＞
+ スモールゴールの達成率＝「完了」タスク　の合計/タスクの合計
+ スモールゴールの進捗度合い=スモールゴールの期日-スモールゴールの着地予想日
+ スモールゴールの着地予想日=該当のスモールゴールに属する「未完了」タスクの想定作業時間の合計を、営業日日付でならした日
+ スモールゴールの進捗=スモールゴールの進捗度合いが0以上なら、「予定通り」。そうでないなら「予定遅れ」

第一正規形はこのようになった。
| エンティティ名 | 属性 | 目的| 
|----|---- |----|
| ユーザー | ID | ユーザーを識別する |
| | 氏名 | ユーザーの名前 |
|  | メールアドレス | 連絡用のアドレス |
| ゴール | ID|ゴールを識別するため|
|  | ユーザーID | ユーザー識別する |
|  | 内容|達成したい目標の内容|
|  | 目標期日|ゴールを達成したい目標の日付|
| スモールゴール | ID|スモールゴールを識別するため|
| | ゴールID|ゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | 目標期日|スモールゴールを達成した目標の日付|
| タスク | ID|タスクを識別するため| 
| |スモールゴールID|スモールゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | ステータス|そのタスクの状況「完了」「未完了」を返す|
|  | 想定作業時間|タスクにかかる想定の作業時間|
| カレンダー |ID|カレンダーを識別するため|
| | ユーザーID | ユーザーを識別する |
| 予定 |ID|予定を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|予定が開始する日時|
|  |終了日時|予定が終了する日時|
| 実際 |ID|実際を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|実際が開始する日時|
|  |終了日時|実際が終了する日時|
| 成長 |ID|成長を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |カテゴリー|成長の種類「学んだこと」「できるようになったこと」を返す|
|  |数量|成長したと思える数|
|  |内容|ユーザー自身が成長したと感じることを書く|

## 第二正規化
+ 部分従属関係の列を別のテーブルに切り分ける
## 主キーとは
+ その値を特定すれば、必ず一行のレコードが取得できる列の組み合わせのこと
+ 主キーがあるということは、「一つのテーブルに全く同じ行はない」ということ

## 私の場合
+ 第二正規化のゴールは、「完全関数従属のみのテーブル」をつくることだ
+ それは、「部分関数従属を無くす」ことで実現できる
+ まずは、現実社会と同じように、実体が異なるものはテーブルに分けた
+ まず現実社会と同じように、実体（エンティティ）が異なるものはテーブルに分けた。ここは感覚的に行った。
+ そのあと、各テーブルに部分関数従属（主キーの一部の列だけで、ある列の値が特定できてしまわないか？）チェックすることで、第二正規化を行った。

| エンティティ名 | 属性 | 目的| 
|----|---- |----|
| ユーザー | ID | ユーザーを識別する |
| | 氏名 | ユーザーの名前 |
|  | メールアドレス | 連絡用のアドレス |
| ゴール | ID|ゴールを識別するため|
|  | ユーザーID | ユーザー識別する |
|  | 内容|達成したい目標の内容|
|  | 目標期日|ゴールを達成したい目標の日付|
| スモールゴール | ID|スモールゴールを識別するため|
| | ゴールID|ゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | 目標期日|スモールゴールを達成した目標の日付|
| タスク | ID|タスクを識別するため| 
| |スモールゴールID|スモールゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | ステータス|そのタスクの状況「完了」「未完了」を返す|
|  | 想定作業時間|タスクにかかる想定の作業時間|
| カレンダー |ID|カレンダーを識別するため|
| | ユーザーID | ユーザーを識別する |
| 予定 |ID|予定を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|予定が開始する日時|
|  |終了日時|予定が終了する日時|
| 実際 |ID|実際を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|実際が開始する日時|
|  |終了日時|実際が終了する日時|
| 成長 |ID|成長を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |カテゴリー|成長の種類「学んだこと」「できるようになったこと」を返す|
|  |数量|成長したと思える数|
|  |内容|ユーザー自身が成長したと感じることを書く|

# 第三正規形
+ 第三正規形とは、第二正規形を満たしていて、かつ非主キーの全ての列が、主キーに対して推移従属関係でないテーブルのこと
+ そのため、まず「主キーの一部の列で他の列が決まってしまわないか」を問うことで、第二正規形を満たしてることを確認した
+ その後、主キーに従属している列をピックアップし、
+ その列に従属している列はないか確認した。
+ つまり「ある非主キーが決まったら、他の列が決まらないか」問うてみて、それがないことを確認できたため、第三正規形を満たしているとした！

| エンティティ名 | 属性 | 目的| 
|----|---- |----|
| ユーザー | ID | ユーザーを識別する |
| | 氏名 | ユーザーの名前 |
|  | メールアドレス | 連絡用のアドレス |
| ゴール | ID|ゴールを識別するため|
|  | ユーザーID | ユーザー識別する |
|  | 内容|達成したい目標の内容|
|  | 目標期日|ゴールを達成したい目標の日付|
| スモールゴール | ID|スモールゴールを識別するため|
| | ゴールID|ゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | 目標期日|スモールゴールを達成した目標の日付|
| タスク | ID|タスクを識別するため| 
| |スモールゴールID|スモールゴールを識別するため|
|  | 内容|ゴールを達成するために必要な目標の内容|
|  | ステータス|そのタスクの状況「完了」「未完了」を返す|
|  | 想定作業時間|タスクにかかる想定の作業時間|
| カレンダー |ID|カレンダーを識別するため|
| | ユーザーID | ユーザーを識別する |
| 予定 |ID|予定を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|予定が開始する日時|
|  |終了日時|予定が終了する日時|
| 実際 |ID|実際を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |タスク|　予定するタスク|
|  |開始日時|実際が開始する日時|
|  |終了日時|実際が終了する日時|
| 成長 |ID|成長を識別するため|
| | ユーザーID | ユーザーを識別する |
|  |カテゴリー|成長の種類「学んだこと」「できるようになったこと」を返す|
|  |数量|成長したと思える数|
|  |内容|ユーザー自身が成長したと感じることを書く|

# 教材のECサイトのDB設計と私が行ったDB設計のどこに違いがあったのか
+ 私が行ったDB設計では、
1.　商品テーブルに商品番号が無かった
2.　教材にはない出荷商品テーブルが存在した
3. 利用者テーブルとスタッフテーブルに分けていた（教材は利用者テーブルのみだった）
4. 1以上 or 0以上のリレーションの違いがあった

# なぜ思いつかなかったのか
## 1. 商品テーブルに商品番号が無かった
+ 商品IDは主キーとして使用するためのDB上の用途のため、サービス利用者が商品を認識する単位として、商品番号は必要
+ DB上で一意のレコードを定めるキーと、現実社会その実態を管理する番号が必要という視点が足りなかったから
+ 主キーを決めたら、実体を管理する番号が別途必要ないか検討する

## 2.　教材にはない出荷商品テーブルが存在した
+ 私は、出荷商品テーブルを設けないと、出荷テーブルが「どの商品を出荷するのか」という情報をどこからも拾えないと考えていた。
+ しかし、出荷する商品は、注文商品と一緒のため、もし出荷商品テーブルを用意してしまうと、出荷商品テーブルの内容にダブりが出てしまう。
+ そこで、出荷商品テーブルは無くすことで、ダブりを無くす。そして、一つの注文に対して出荷を行う関係にあるため、注文と出荷を一対１で紐付ける
+ 「一つの注文は複数の注文商品を持つ」親子関係で紐づいているため、親である注文からは注文商品は参照できる
+ このことを利用して、出荷⇨注文⇨注文商品を辿ることができるため、出荷商品テーブルを設けなくても、「どの商品を出荷するのか」を拾うことできるとわかった
+ 「同じ実体を参照する時に、リレーションを結ぶことで解決できないか」という視点がたりなった

## 3. 利用者テーブルとスタッフテーブルに分けていた（教材は利用者テーブルのみだった）
+ 私は、利用者とスタッフはそもそも異なる実体のため、テーブルも分けるのが普通だと考えていた。
+ しかし教材では、利用者（顧客）とスタッフでは、従業員番号以外の属性は共通しているため、利用者に抽象化して、利用者の中でその利用者が「従業員」なのか「顧客」なのか分ける属性をもつようにしていた。
+ これはプログラミングの共通処理を関数にして、異なる部分を引数で渡す考え方と近くて、DBでも同じことなんだと腑に落ちた
+ 利用者という実体は、利用者タイプの属性に入る値に応じて、「顧客」になったり、「スタッフ」になったり、実体のあり方を変えるイメージだ！
+ テーブル間で属性が重複している時に、実体を抽象化できないか考える

## 4. 1以上 or 0以上のリレーションの違いがあった
+ 例えば私は、一つの商品は、一つの在庫情報を持つと考えていたため、１対１で結んでいた
+ しかし、教材では1対0or1としていた
+ 理由は商品は在庫を持っていない可能性があるからとのこと
+ それは私自身理解していたが、その場合は在庫の数量属性が0になるだけで、商品は必ず一つの在庫情報を持つという関係には関係ないのでは？と考えていた。
+ でもそうゆうことじゃないのがわかった。リレーションとはある実体が別の実体と何体何の関係になり得るのかを示したもの
+ だから「在庫が0であれ、商品は在庫の情報を必ず一つ持つから、リレーションは1対1」は間違い
+ 何対何を特定するときは、実体と実体が何対何の関係になるか考える
 
## ER図とは何か?
+ 実体どうしの関係性を図示したもの
+ 実体とは、テーブルのレコードだと思えばよい

# 以上の学びはPFにどう活きたのか?
+ 実際にER図と属性を作成することができた！
<img width="1440" alt="スクリーンショット 2022-07-01 10 21 42" src="https://user-images.githubusercontent.com/74813444/176804135-9d9fa21e-494a-4df2-93dd-9cbcfe021f18.png">

+ 活きた点としては、まず予定と実際のエンティティで属性がかぶっていたため、イベントというエンティティで抽象化できた！
+ さらに、「あるタスクは0以上のイベントを持つ」ため、１対0or多のリレーションを結ぶことに気づけた！
+ 勿論、完成ではないはずだが現時点でのER図はこんな感じになった！
