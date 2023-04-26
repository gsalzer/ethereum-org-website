---
title: スマートコントラクトの形式的検証
description: イーサリアムのスマートコントラクトのための形式的検証の概要
lang: ja
---

[スマートコントラクト](/developers/docs/smart-contracts/) は分散型でトラストレスかつ堅牢なアプリケーションの作成を可能とし、それによってユーザーに対して新しいユースケースを導入し、さらなる価値を引き出します。 スマートコントラクトが取り扱うものには巨額の価値があるため、デベロッパーにとってセキュリティは極めて重要です。

形式的検証は[スマートコントラクトのセキュリティ](/developers/docs/smart-contracts/security/)を向上させるために推奨される技術のひとつです。 形式的検証は、[形式手法](https://www.brookings.edu/techstream/formal-methods-as-a-path-toward-better-cybersecurity/)によってプログラムの仕様記述、設計および検証を行うもので、極めて重要性の高いハードウェアやソフトウェアシステムの正しさを保証するのに以前から使われてきました。

スマートコントラクトに形式的検証を実施することで、スマートコントラクトの挙動があらかじめ規定された仕様を満たすことが証明できます。 スマートコントラクトのコードの正しさを査定する他の手法(テスト技法など)と比べて、形式的検証はスマートコントラクトが機能的に正しいことをより強力に保証します。

## 形式的検証とは何か {#what-is-formal-verification}

形式的検証というのは、形式仕様記述に対してシステムの正しさを評価するための一連の作業のことです。 簡単に言うと、形式的検証によってシステムの挙動が求められている要件を満たしているかどうかを確かめることができます。

システム(この場合はスマートコントラクト)の期待される挙動は形式的な表現によって記述され、形式的プロパティは仕様記述言語で書かれます。 そして、形式的検証の技法により、スマートコントラクトの実装が仕様に準拠していることを検証でき、さらにその実装の正しさの数学的証明を導くことができます。 スマートコントラクトが仕様を満たすことを、「機能的に正しい」、「設計により正しい」または「構成により正しい」といいます。

### 形式モデルとは何か {#what-is-a-formal-model}

計算機科学において、 [計算の形式モデル](https://en.wikipedia.org/wiki/Model_of_computation)は計算プロセスを数学的に記述するものです。 プログラムは数学的な関数(方程式)によって抽象化され、関数の入力に対して出力をどう計算するかが形式モデルによって記述されます。

形式モデルは、プログラムの挙動の分析を評価できる水準での抽象化を可能とします。 形式モデルがあることで*形式仕様*が作成でき、それによって、問題となっているモデルの持つべき望ましいプロパティが記述されます。

形式的検証のためのスマートコントラクトのモデル化には、さまざまな技法が用いられます。 たとえば、スマートコントラクトの高水準な挙動を論証するために用いられるモデルがあります。 そのようなモデル化技法では、スマートコントラクトを与えられた入力に基いて計算を実行するブラックボックスとみなします。

高水準なモデルでは、スマートコントラクトと外部エージェント(外部所有アカウント(EOA)、コントラクトアカウントもしくはブロックチェーン環境)との関係に重点が置かれます。 このようなモデルは、スマートコントラクトが特定のユーザーインタラクションにどう応じるべきかを記述するのに有用です。

逆に、スマートコントラクトの低水準な挙動に重点を置く形式モデルもあります。 高水準なモデルはスマートコントラクトの機能について論証するのに役立ちますが、その反面、実装の内部動作の詳細が上手く掴めないこともあります。 低水準なモデルのプログラム解析ではプログラムをホワイトボックスとみなし、スマートコントラクトの実行に関連するプロパティの論証では、プログラムトレースや[制御フローグラフ](https://en.wikipedia.org/wiki/Control-flow_graph)など、スマートコントラクトアプリケーションの低水準な表現を利用します。

低水準なモデルはイーサリアムの実行環境([EVM](/developers/docs/evm/))における実際の実行を表すため、理想的であると考えられています。 低水準なモデリング技法は、スマートコントラクトの重要な安全性の確立と潜在的な脆弱性の検出に特に有用です。

### 形式仕様記述とは何か {#what-is-a-formal-specification}

仕様とは、特定のシステムが満たすべき技術的要件のことです。 プログラミングにおいては、仕様はプログラムの実行についてのおおまかな狙い(すなわち、プログラムが何をすべきか)を表します。

スマートコントラクトの文脈では、形式仕様記述はスマートコントラクトが満たさなければならない要件を形式的に記述したものであり、これを*プロパティ*と呼びます。 プロパティはスマートコントラクトの実行を通しての「不変条件」として記述され、例外なしにあらゆる状況下で真であるべき論理アサーションを表現するものです。

したがって、形式仕様記述はスマートコントラクトのしかるべき挙動を形式言語で記述したものの集まりである、と考えることができます。 仕様が対象とするのはスマートコントラクトのプロパティで、スマートコントラクトがさまざまな状況下でどのように動作すべきかを表します。 スマートコントラクトがそれらのプロパティ(不変条件) を実行中に破らないことを見極めるのが、 形式的検証の目的です。

形式仕様記述は、スマートコントラクトのセキュアな実装をする上で極めて重要です。 求められた不変条件を満たさなかったり、実行中にプロパティを破るスマートコントラクトには、その機能を害したり悪用を許してしまう脆弱性があるおそれがあります。

## スマートコントラクトの形式仕様記述の種類 {#formal-specifications-for-smart-contracts}

形式的仕様記述により、プログラム実行の正しさを数学的に論証することが可能となります。 形式モデルと同様、形式仕様記述は高水準のプロパティもしくはスマートコントラクトの実装の低水準の挙動をとらえることができます。

形式仕様記述はプログラム論理を用いて導出され、それによってプログラムのプロパティが形式的に論証できるようになります。 プログラム論理が備えている形式規則により、プログラムのしかるべき挙動が(数学的に)表現されます。 [到達可能性論理 ](https://en.wikipedia.org/wiki/Reachability_problem)、[時相論理](https://en.wikipedia.org/wiki/Temporal_logic)、[ホーア論理](https://en.wikipedia.org/wiki/Hoare_logic)を含む様々なプログラム論理が形式仕様記述に用いられます。

スマートコントラクトの形式仕様記述は**高水準**な仕様記述と**低水準**な仕様記述に大別されます。 いずれの場合でも、分析対象のシステムのプロパティを適切かつ一義的に記述できなければなりません。

### 高水準な仕様記述 {#high-level-specifications}

その名のとおり、高水準な仕様記述(「モデル指向仕様記述」とも呼ばれます) はプログラムの高水準な挙動を記述するためのものです。 高水準な仕様記述においてスマートコントラクトは[有限状態機械](https://en.wikipedia.org/wiki/Finite-state_machine)(FSM)としてモデル化されます。スマートコントラクトの挙動は処理の実行を伴う状態遷移で表され、モデルの形式的プロパティは時相論理で記述されます。

[時相論理](https://en.wikipedia.org/wiki/Temporal_logic)は時間という条件の付いた命題(たとえば、「私は*いつも*空腹だ」や「私は*いずれ*空腹になる」)を論証するための規則の集まりです。 形式的検証での時相論理は、状態遷移機械でモデル化されたシステムの正しい挙動に関するアサーションを記述するために使われます。 具体的には、スマートコントラクトがある将来の時点でモデルのどの状態にあるのか、そしてどのように状態間で遷移するのかを時相論理で記述できます。

高水準な仕様記述は一般に**安全性 (safety)**と**活性 (liveness)**という、スマートコントラクトの時相に関する重要な 2 つのプロパティを捕えることができます。 安全性とはすなわち「何も悪いことが起こらない」ということで、通常は不変条件によって表されます。 安全性は、[デッドロック](https://www.techtarget.com/whatis/definition/deadlock)が起こらないことといった一般的なソフトウェア要件を意味することもあれば、スマートコントラクトのドメイン固有なプロパティ(たとえば関数に対するアクセス制御の不変条件、状態変数の許容値、トークン転送の条件など)を意味することもあります。

例として、*「送金しようとしているトークンの量に対し、送金元の口座残高に不足があってはならない」*という、 ERC-20 トークンのスマートコントラクトで`transfer()`や`transferFrom()`を呼び出す際の安全性の要求仕様を考えます。 この自然言語で書かれたスマートコントラクトの不変条件は数学的な形式仕様記述に翻訳され、その妥当性が厳密に検査されます。

活性は「何らかの良いことがいずれは起こる」ということを意味し、スマートコントラクトに関しては、するべき処理を滞りなく進められることを示します。 活性の一例として「流動性」が挙げられ、これは、要求に応じてスマートコントラクトが残高を他ユーザーに譲渡できることを意味します。 このプロパティが破られると、[パリティウォレット事件](https://www.cnbc.com/2017/11/08/accidental-bug-may-have-frozen-280-worth-of-ether-on-parity-wallet.html)で起こったように、あるはずの資産が引き出せなくなります。

### 低水準な仕様記述 {#low-level-specifications}

高水準な仕様記述では、有限状態モデルから出発してモデルの望ましいプロパティを記述しました。 一方で、低水準な仕様記述(プロパティ指向仕様記述とも呼ばれます)では、プログラム(すなわちスマートコントラクト)を数学的な関数の集まりからなるシステムとしてモデル化し、そのシステムの正しい挙動を記述します。

簡単に言うと、低水準な仕様記述では*プログラムのトレース* を解析し、それに基づいてスマートコントラクトのプロパティを記述することを目指します。 トレースとは、スマートコントラクトの状態変化を伴った関数の実行シーケンスを指します。したがって、低水準な仕様記述ではスマートコントラクト内部の実行の様子についての要求仕様まで記述できます。

低水準な仕様記述は、ホーア型のプロパティもしくはプログラムの実行フローによって与えられます。

### ホーア型のプロパティ {#hoare-style-properties}

[ホーア論理](https://en.wikipedia.org/wiki/Hoare_logic)は、スマートコントラクトなどのプログラムの正当性を論証するための形式規則を提供します。 ホーア型のプロパティは、ホーアトリプルと呼ばれる{_P_}_c_{_Q_}という形の式で与えられます。*c*はプログラムで、*P*と*Q*はそれぞれ正式には*事前条件*、*事後条件*と呼ばれる、c の状態についての述語です。

事前条件は関数を正しく実行するために求められる条件を表しています。スマートコントラクトを呼び出す際には、この事前条件が満たされている必要があります。 事後条件は、関数が正しく実行された場合に成立する条件を表しています。関数呼び出しの後はこの事後条件が真となることが期待されます。 ホーア論理の*不変条件*は、関数の実行中は常に維持されます(すなわち、変化しません)。

ホーア型の仕様記述により、*部分正当性*もしくは*完全正当性*が保証されます。 事前条件が関数実行前に真であり、実行が停止した場合には事後条件も真であるとき、スマートコントラクト関数の実装は「部分正当性を満たし」ます。 完全正当性を証明するには、関数の実行前に事前条件が真であること、実行が終了すること、その際に事後条件が真であることの三つが必要です。

プログラムによっては実行が終わるのに時間がかかったり、そもそも終わらなかったりするので、完全正当性の証明は困難となります。 とはいえ、イーサリアムのガスメカニズムが無限ループを防ぐので、実行が終了するかどうかは議論の余地はあれども理論上の問題点です(実行は正常に終了するか、'ガス不足'のエラーで打ち切られます)。

ホーア論理で作成されたスマートコントラクトの仕様記述は、スマートコントラクト内部の関数やループを実行するために述べられた事前条件、事後条件、不変条件を含みます。 事前条件が関数への誤った入力を許容する場合もあり、その際は事後条件でそのような誤った入力があった場合の応答(例外を発生させるなど)が記述されます。 このようにして、ホーア型のプロパティによってスマートコントラクト実装の正当性を保証する際に有効となります。

多くの形式的検証のフレームワークで、関数の意味的正当性を証明するためにホーア型の仕様記述が使われています。 Solidity の`require`と`assert`ステートメントを使えば、スマートコントラクトのコードにホーア型のプロパティを(アサーションとして) 直接書くこともできます。

`require`ステートメントは事前条件もしくは不変条件を書くのに用いられ、ユーザーから与えられた入力の有効性を確認するのに使われることが多く、その一方で`assert`ステートメントは安全性のために必要な事後条件を書くのに使われます。 たとえば、 関数への適切なアクセス制御(安全性プロパティの一例) は、`require`ステートメントを使用して、該当する関数を呼び出そうとしているアカウントの識別情報を前提条件チェックすることで実現できます。 同様に、`assert`ステートメントで実行後のスマートコントラクトの状態を確認することで、コントラクト内部の状態変数(たとえば、流通しているトークンの総数など)が許容される値であることを示す不変条件を違反しないが保護することがであることを示す不変条件の違反を防ぐことができます。

### トレースレベルのプロパティ {#trace-level-properties}

トレースベースの仕様記述では、スマートコントラクトの状態を遷移する操作や、操作間にある関係を定義します。 前述したように、トレースは特定の方法でスマートコントラクトの状態を変化させる操作シーケンスです。

このアプローチは、スマートコントラクトのモデルがいくつかの定義済みの状態(状態変数で表されます) および遷移(スマートコントラクトの関数で表されます) を備えた状態遷移系で与えられていることを前提としています。 さらに、 スマートコントラクトの操作的意味論を記述するには、プログラムの実行の流れを図示する[制御フローグラフ](https://www.geeksforgeeks.org/software-engineering-control-flow-graph-cfg/) (CFG) がよく用いられます。 ここで、各トレースは制御フローグラフ上のパスで表されます。

主に、トレースレベルの仕様記述は、スマートコントラクトの内部の実行のパターンを論じる際に用いられます。 トレースレベルでの仕様記述を作成することで、スマートコントラクトに許容される実行パス(すなわち、状態遷移)をアサートできます。 シンボリック実行などの手法を用いれば、形式的モデルで定義されていないパスには実行が進まないことを形式的に検証することができます。

例として、公開されている関数でトレースレベルのプロパティの記述ができる[DAO](/dao/)コントラクトを見てみましょう。 ここでは、DAO コントラクトによってユーザーが以下の操作を実行できることとします。

- 入金

- 入金後に、ある提案に対し投票をする

- 提案への投票をしなかった者は返金を要求する

トレースレベルのプロパティの例として、 _「資金を入金しないユーザーは、提案に投票できない」_ または *「提案に投票しないユーザーは、いつでも返金を要求できる」*などが考えられます。 どちらのプロパティも望ましい実行シーケンスをアサートします(入金する*前*に投票することはできず、提案に投票した*後*に払い戻しの要求はできません)。

## スマートコントラクトの形式的検証の技法 {#formal-verification-techniques}

### モデル検査 {#model-checking}

モデル検査は、仕様記述に対し、アルゴリズムによってスマートコントラクトの形式的モデルの検査を行う形式的検証の技法です。 モデル検査では通常、スマートコントラクトは状態遷移システムとして表され、許容されるコントラクトの状態のプロパティは時相論理を用いて定義されます。

モデル検査では、システム(すなわち、スマートコントラクト)を抽象的かつ数学的に表すことと、システムが満たすべきプロパティを[命題論理](https://www.baeldung.com/cs/propositional-logic)に根差した論理式で表すことが求められます。 これにより、モデル検査アルゴリズムの目的は、数学的モデルが与えられた論理式を充足することの証明、と単純化されます。

形式的検証におけるモデル検査は主として、スマートコントラクトの挙動を時間経過を加味して記述する時間的プロパティの評価に用いられます。 そのような、時間が関係するスマートコントラクトのプロパティには、先に説明した*安全性*と*活性*が含まれます。

たとえば、アクセス制御(たとえば、 *スマートコントラクトの所有者に限り`selfdestruct`*を呼び出すことができます)に関するセキュリティプロパティが形式論理で記述できます。 その後、モデル検査のアルゴリズムはスマートコントラクトがこの形式的仕様を満たしているかどうかを検証することができます。

モデル検査では、スマートコントラクトのすべての起こりうる状態を正確に描き、そのうちの到達可能な状態を見つけだそうと試みることで、状態空間の探索をします。 しかし、これは無限の状態数につながる可能性があります(「状態爆発問題」として知られています)。そのため、モデル検査器は抽象化技法によってスマートコントラクトの分析を効率化します。

### 定理証明 {#theorem-proving}

定理証明は、スマートコントラクトなどのプログラムの正しさを数学的に論証する手法です。 スマートコントラクトのシステムとその仕様記述を数式(論理式)で書き下す必要があります。

定理証明で目指すのは、それらの数式が論理的に等価であると検証することです。 「論理的な等価性」(「論理的な双含意」とも呼ばれます)は二つのステートメント間の関係の一種で、片方が真の*時かつその時に限り*もう片方も真となることです。

スマートコントラクトのモデルに関するステートメントとそのプロパティのステートメントとの間に要求された関係性(論理的等価性)は、証明可能なステートメント(定理と呼ばれます)として定式化されます。 形式的な推論システムにより、自動定理証明器は定理の妥当性を検証することができます。 言い換えれば、定理証明器はスマートコントラクトのモデルが仕様記述に正確に適合していることを、疑いの余地のない形で証明することができます。

モデル検査でスマートコントラクトが有限状態の遷移システムでモデル化されていたのに対し、定理証明では無限に状態を持つシステムの分析が可能となります。 しかし、 自動証明器があらゆる問題について「決定可能」かどうかを判断できるわけではないことをも意味します。

結果、定理証明器が正しさの証明を導出するためには、多くの場合、人による作業も必要となります。 モデル検査が完全に自動化されているのに対し、人手が必要となる定理証明にはより高いコストがかかります。

### シンボリック実行 {#symbolic-execution}

シンボリック実行は、関数の実行にあたって*具体的な値*(例えば`x == 5`)の代わりに*シンボリックな値*(例えば`x > 5`)を用いることで、スマートコントラクトの分析を行う手法です。 形式的検証の手法として、シンボリック実行によってスマートコントラクトのコード中のトレースレベルのプロパティを形式的に論証することができます。

シンボリック実行では、実行トレースはシンボリックな入力値を含む数式で表され、これを*パス述語*と呼びます。 [SMT ソルバー](https://en.wikipedia.org/wiki/Satisfiability_modulo_theories)はパス述語が「充足可能」(すなわち、論理式を満たす値が存在する)かどうかを検査するのに用いられます。 脆弱性のあるパスが充足可能であれば、SMT ソルバーはそのようなパスへと実行を進めてしまう具体的な値を生成します。

入力として`uint`の値(`x`)を取り、`x`が`5`より大きく、`10`より小さい場合にリバートするスマートコントラクトの関数を考えます。 通常のテストの手順では、エラーとなる`x`の値を求めるのに、実際にそのような値が見つかる確信のないまま数十(もしくはそれ以上の)テストケースを実行することになります。

逆に、シンボリック実行のツールは`X > 5 ∧ X < 10`(つまり、`x`は 5 より大きく、かつ、`x`は 10 より小さい)といったシンボリックな値を用いて関数を実行します。 関連するパス述語`x = X > 5 <unk> X < 10`は、これを解くために SMT ソルバーに渡されます。 ある値が論理式`x = X > 5 ∧ X < 10`を満たすのであれば、SMT ソルバーはそのような値を計算します。たとえば、`x`の値として`7`を出してくるかもしれません。

シンボリック実行はプログラムへの入力によって行うもので、到達可能なあらゆる状態を探索するための入力集合は潜在的に無限となるため、これは依然としてテスト技法の一形態です。 ただし、例に示すように、プロパティに反する入力を見付けることについては、シンボリック実行は通常のテスト技法より効果的です。

さらに、シンボリック実行は、関数への入力をランダム生成するようなプロパティベースの技法(ファジングなど)よりも偽陽性が少なくなります。 シンボリック実行でエラー状態に到達した場合は、そのエラーを引き起こす具体的な入力値を生成して問題を再現することができます。

シンボリック実行により、正しさに関するある程度の数学的証明を作成することもできます。 次のような、オーバーフローが防止されたスマートコントラクトの関数を例に考えます。

```
function safe_add(uint x, uint y) returns(uint z){

  z = x + y;
  require(z>=x);
  require(z>=y);

  return z;
```

整数のオーバーフローを起こす実行トレースは、`z = x + y AND (z >= x) AND (z=>y) AND (z < x OR z < y)`という論理式を満たす必要があります。そのような論理式は解けそうにもなく、`safe_add`関数が決してオーバーフローしないことの数学的証明としての役目を果たします。

### なぜスマートコントラクトを形式的に検証するのか {#benefits-of-formal-verification}

#### 信頼性の必要性 {#need-for-reliability}

形式的検証は、システム障害によって死傷者を出したり金融破綻を引きおこすなどの破滅的な結果となりうる、セーフティークリティカル・システムの正しさを評価するのに使用されます。 スマートコントラクトは膨大な価値を管理するアプリケーションであり、設計上の単純なエラーが[回復不可能な損失](https://www.freecodecamp.org/news/a-hacker-stole-31m-of-ether-how-it-happened-and-what-it-means-for-ethereum-9e5dc29e33ce/amp/)を引き起こすことになります。 デプロイする前にスマートコントラクトの形式的検証をすることで、ブロックチェーン上で期待通りに動作する保証が高まります。

特にイーサリアム仮想マシン(EVM)にデプロイされたコードは通常は変更不能であるため、あらゆるスマートコントラクトにおいて信頼性は非常に望ましいことです。 スマートコントラクトは起動後は容易にアップグレードできないので、信頼性の保証の需要により形式的検証が必要とされています。 整数のアンダーフローやオーバーフロー、リエントランスや不十分なガス最適化など、過去に監査担当者やテスターを躓かせてきた扱いにくい問題であっても、形式的検証で検出することができます。

#### 機能的正確性の証明 {#prove-functional-correctness}

プログラムテスト技法は、スマートコントラクトがある要求を満たしていることを証明する最も一般的な方法です。 プログラムテストでは、スマートコントラクトに正常に動作することが期待されるサンプルデータを与えて実行し、そのときの挙動を分析します。 期待通りの結果であれば、それが正しさの客観的な証明となります。

しかし、このアプローチではサンプルに含まれない入力値に関しては正しさを証明することができません。 そのため、スマートコントラクトをテストすることでバグを検出できますが(すなわち、あるコードパスが実行中に望まれる結果を返さないならば)、**バグがないことを疑いの余地のない形で証明することはできません**。

逆に、形式的検証によって、スマートコントラクトが実行範囲が無限となるような要求を満たすことを、実行すること*なし*に、形式的に証明することができます。 そのためには、スマートコントラクトの正しい挙動の詳細な形式仕様記述を作成し、スマートコントラクトのシステムの形式的(数学的)モデルを開発する必要があります。 そうすれば、形式的な証明手続きによって、スマートコントラクトのモデルとその仕様記述の間に矛盾がないことを確認することができます。

形式的検証により、スマートコントラクトのビジネスロジックが要件を満たしているかどうかを検証するという問題は、証明もしくは反証できる数学的命題となります。 形式的に命題を証明することで、無限にあるテストケースが有限の手順で検証できるようになります。 形式的検証には、スマートコントラクトが仕様記述に対して機能的に正しいこと証明することの、より良い将来性が見込まれます。

#### 検証の理想的な対象 {#ideal-verification-targets}

検証の対象は、形式的に検証すべきシステムを記述します。 形式的検証は、「組み込みシステム」(大きなシステムの一部を形成する小さく単純なソフトウェア)で最もよく使用されます。 また、少数の規則のみを扱うドメインであれば、ドメイン固有のプロパティを検証するためにツールを変更することも容易であるため、形式的検証が適しています。

スマートコントラクトは、少なくともある程度は両方の要件を満たしています。 たとえば、イーサリアムのスマートコントラクトはサイズが小さいため、形式的検証が適しています。 同様に、EVM は単純なルールに従っているため、EVM で実行されるプログラムの意味論的なプロパティの仕様記述とその検証も容易になります。

### 開発サイクルの短縮 {#faster-development-cycle}

モデル検査やシンボリック実行などの形式的検証の技法は、一般的にスマートコントラクトのコードの定期的な分析よりも効率的です(テストや監査中に実行されます)。 これは、形式的検証ではアサーションをテストするためにシンボリックな値を用いるからで(「ユーザーが _n_ eth 引き出そうとした場合はどうなりますか?」)、 具体的な値を用いる場合(「ユーザーが 5 eth を引き出そうとした場合はどうでしょう？」)とは異なります。

シンボリックな入力変数は複数クラスの具体的値をカバーできるため、形式的検証のアプローチでは、より短い時間でより多くのコードカバレッジが期待されます。 形式的検証を効果的に使用することで、開発サイクルを加速させることができます。

形式的検証は、コストのかかる設計エラーを減らすことで、分散型アプリケーション(dapps)を構築するプロセスも改善します。 脆弱性を修正するためにスマートコントラクトをアップグレードするには、コードベースの広範な書き換えと多くの労力が必要です。 形式的検証は、過去にテスターや監査担当者をつまずかせてきたスマートコントラクト実装のエラーを検出することができ、スマートコントラクトのデプロイ前に問題を修正する十分な機会を用意します。

## 形式的検証の難点 {#drawbacks-of-formal-verification}

### 手作業のコスト {#cost-of-manual-labor}

形式的検証、特に半自動検証のように人が証明ツールを操作して正しさの証明を得るものは、かなりの手作業を要します。 さらに、形式仕様記述の作成は複雑な作業であり、高度なスキルが要求されます。

そのような要因(労力とスキル)により、形式的検証はテスト技法や監査など、スマートコントラクトの正しさを査定するための通常の手法に比べて多大な努力を要し、高コストとなります。 それにもかかわらず、スマートコントラクトの実装にエラーがあった場合の損害を考慮し、完全な検証監査のため実際にコストがかけられています。

### 偽陰性 {#false-negatives}

形式仕様検証で確認できるのは、スマートコントラクトの実行が形式仕様記述に一致するかどうかのみです。 そのため、スマートコントラクトに求められる挙動が正しく仕様記述されていることが重要です。

もし仕様記述に不備があれば、形式的検証監査ではプロパティが破られることを検出できず、これはすなわち脆弱性のもととなります。 この場合、デベロッパーにスマートコントラクトにバグがないと勘違いさせてしまうことになります。

### パフォーマンスの問題 {#performance-issues}

形式的検証はパフォーマンス上の問題になります。 例えば、モデル検査とシンボル検査中に発生する状態爆発問題とパス爆発問題により、検証手順に悪影響がありえます。 また、形式的検証ツールは多くの場合に SMT ソルバーやその他の制約ソルバーを内部的に使用しており、多量の計算処理を要します。

プログラムが停止しない可能性([decidability problem](https://en.wikipedia.org/wiki/Decision_problem))もあるため、要求されるプロパティ(論理式として定義される)が満たされるのかどうかも一般には判断できません。 スマートコントラクトのプロパティが正しく記述されていたとしても、証明することが不可能なことがあります。

## イーサリアムのスマートコントラクトのための形式的検証ツール {#formal-verification-tools}

### 形式的仕様記述のための言語 {#specification-languages}

**Act**: \_\*ストレージの更新、事前条件・事後条件、スマートコントラクトの不変条件を仕様として記述できます。 Act ツールスイートには、Coq、SMT ソルバー、hevm を介してプロパティを証明するための、証明バックエンドが含まれます。\*\*

- [GitHub](https://github.com/ethereum/act)
- [ドキュメント](https://ethereum.github.io/act/)

**Scribble** - \_\*Scribble は、Scribble 仕様言語のコードアノテーションを具体的なアサーションに変換し、仕様記述を検査することができます。\*\*

- [ドキュメント](https://docs.scribble.codes/)

**Dafny** \_\*Dafny はプログラム検証との親和性が高いプログラミング言語で、その高水準なアノテーションを用いることでコードの正しさを論証したり証明したりすることができます。\*\*

- [GitHub](https://github.com/dafny-lang/dafny)

### 正しさを確認するためのプログラム検証器 {#program-verifiers}

**Certora Prover** _Certora Prover は、スマートコントラクトのコードの正しさを検査するための自動形式的検証ツールです。 仕様記述は CVL(Certora Verification Language)で行い、静的解析と制約解析の組み合わせでプロパティ違反を検出します。_

- [ウェブサイト](https://www.certora.com/)
- [ドキュメント](https://docs.certora.com/en/latest/index.html)

**Solidity SMTChecker** \_\*Solidity SMTChecker は、SMT(Satisfiability Modulo Theories)とホーン節の充足問題に基づく組み込みのモデルチェッカーです。 コンパイル中にスマートコントラクトのソースコードが仕様記述に適合することを確認し、安全性プロパティの違反があるかどうかを静的にチェックします。\*\*

- [GitHub](https://github.com/ethereum/solidity)

**solc-verify** \_\*solc-verify は Solidity コンパイラの拡張で、アノテーションとモジュラー型のプログラム検証によって自動的に形式的検証を行います。\*\*

- [GitHub](https://github.com/SRI-CSL/solidity)

**KEVM** \_\*KEVM は、K framework で書かれたイーサリアム仮想マシン(EVM)の形式的な意味論です。 KEVM は実行可能であり、到達可能性論理によってある種のプロパティ関連のアサーションの証明に利用できます。\*\*

- [GitHub](https://github.com/runtimeverification/evm-semantics)
- [ドキュメント](https://jellopaper.org/)

### 定理証明のための論理枠組み {#theorem-provers}

**Isabelle** _証明支援ソフトウェア Isabelle/HOL は、数式を形式言語で表現したり、それらを証明するためのツールで構成されます。 主に数学における証明の形式化とある種の形式的検証に用いられ、コンピュータハードウェアやソフトウェアの正しさの証明やコンピュータ言語やプロトコルの性質の証明などに応用されています。_

- [GitHub](https://github.com/isabelle-prover)
- [ドキュメント](https://isabelle.in.tum.de/documentation.html)

**Coq** _Coq は対話型の定理証明器で、機械的に検査されたプログラムの正しさの証明を対話的に作成することができます。_

- [GitHub](https://github.com/coq/coq)
- [ドキュメント](https://coq.github.io/doc/v8.13/refman/index.html)

### シンボリック実行に基いてスマートコントラクトが脆弱となるパターンを検出するツール {#symbolic-execution-tools}

**Manticore** \_\*シンボリック実行に基づいた EVM バイトコードの解析ツールです。\*\*

- [GitHub](https://github.com/trailofbits/manticore)
- [ドキュメント](https://github.com/trailofbits/manticore/wiki)

**hevm** \_\*hevm はシンボリック実行のエンジンで、EVM バイトコードの同等性を検査することができます。\*\*

- [GitHub](https://github.com/dapphub/dapptools/tree/master/src/hevm)

**Mythril** _イーサリアムスマートコントラクトの脆弱性を検出するシンボリック実行のツールです。_

- [GitHub](https://github.com/ConsenSys/mythril-classic)
- [ドキュメント](https://mythril-classic.readthedocs.io/en/develop/)

## 参考文献 {#further-reading}

- [スマートコントラクトの形式的検証方法](https://runtimeverification.com/blog/how-formal-verification-of-smart-contracts-works/)
- [形式的検証で完璧なスマートコントラクトを実現する方法](https://media.consensys.net/how-formal-verification-can-ensure-flawless-smart-contracts-cbda8ad99bd1)
- [イーサリアム・エコシステムにおける形式的検証プロジェクトの概要](https://github.com/leonardoalt/ethereum_formal_verification_overview)
- [イーサリアム 2.0 の入金スマートコントラクトのエンドツーエンドな形式的検証](https://runtimeverification.com/blog/end-to-end-formal-verification-of-ethereum-2-0-deposit-smart-contract/)
- [世界一有名なスマートコントラクトの形式的検証](https://www.zellic.io/blog/formal-verification-weth)
- [SMTChecker と形式的検証](https://docs.soliditylang.org/en/v0.8.15/smtchecker.html)