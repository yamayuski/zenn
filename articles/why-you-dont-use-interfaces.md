---
title: "何故あなたはインターフェースをうまく使えないのか"
emoji: "🏗"
type: "tech"
topics: ["interface","architecture"]
published: true
---

## 結論

**インターフェースは世界で唯一のアプリケーションのためではなく、汎用的なライブラリ・フレームワークのための機能だから** です。

## アプリケーションとライブラリ

プロダクトは大きく **「アプリケーション・サービス」と「ライブラリ・フレームワーク」** に大別出来ます。前者は特定の需要を満たすために作られるそれ専用のプロダクトです。具象と言うことも出来るでしょう。後者はアプリケーションを作るために利用される部品や骨組みです。

オブジェクト指向言語によく存在している「インターフェース」は、 **「具象の形状の宣言」** です。例えば、下記のようなものがあります。

```php
interface ClockInterface
{
  public function now(): \DateTimeImmutable;
}
```

これは [PSR-20](https://www.php-fig.org/psr/psr-20/) の ClockInterface です。 `now` というメソッドが `\DateTimeImmutable` クラスを返却することが「宣言」されています。

インターフェースには基本的に実装（具象）がありません。実装は別の場所（ライブラリ・フレームワーク・アプリケーション）に移譲しているわけですね。

この PSR は「ライブラリ」としてパッケージ化され、依存関係に追加することが出来ます。そして、別のパッケージで実装され、アプリケーションで利用されます。

```php
readonly final class RegisterUserUseCase
{
  public function __construct(
    private ClockInterface $clock,
    private UserRepositoryInterface $repo,
  ) {
  }

  public function __invoke(
    string $user_name,
  ): User {
    return $repo->register(
      user_name: $user_name,
      created_at: $clock->now(),
    );
  }
}
```

これは PHP によるユーザー登録のコード事例ですが、 `ClockInterface` がコンストラクタインジェクションで宣言されていますね。ここで、 `ClockInterface` の実装がいったいどのようにされているか、 `RegisterUserUseCase` クラスは全く知らずに利用することが出来ます。

例えばリクエストを受け取った時間で固定され、常にその日時を返却する `FixedClock` 具象クラスかもしれませんし、 `\DateTimeImmutable` を継承した `Chronos` クラスを返却する `ChronosClock` 具象クラスかもしれません。しかし、 `RegisterUserUseCase` を実装する人間は、その具象クラスを全く意識することがありません。何故なら、今欲しいのは `\DateTimeImmutable` 具象クラスを返却してくれる何か、だけだからです。これが「アプリケーション」です。

少し話がそれますが、例えばこう書くとどうなるでしょうか。

```php
readonly final class RegisterUserUseCase
{
  public function __invoke(
    string $user_name,
  ): User {
    $clock = new SystemClock();
    $repo = new UserRepository();
    return $repo->register(
      user_name: $user_name,
      created_at: $clock->now(),
    );
  }
}
```

こう書くことも出来るでしょう。では、この場合 `created_at` をユニットテストで指定出来るでしょうか？

`SystemClock` の static プロパティに日時が設定されていて、 static メソッドでそれを指定出来れば問題ないかもしれませんが、そういった実装になっていない場合はユニットテストで created_at が適切に指定した日時かどうかを区別することが出来ません。そのため、コンストラクタインジェクションによる移譲を使うわけですね。

## 「アプリケーション」は具象、「ライブラリ・フレームワークは抽象」

皆さんがプロダクトを作成する時は、ほとんどの実装は **「具象」** です。実際に要件通りユーザーを登録したり、スコアを記録したりします。対して、ライブラリやフレームワークは「ユーザー」という具象を定義しません。「ユーザー」がパスワードを持っていること、 ID を持っていること、という **「宣言」** はするかもしれませんが、「ユーザー名を持っていること」「複数のグループに所属すること」などは具象であるアプリケーションしかわからない領域です。

```php
interface HasPassword
{
  public function getHashedPassword(): string;
}
```

ライブラリやフレームワークはこのように **「抽象」** を定義して、アプリケーションが具象を実装してくれる想定で汎用処理を実装します。

```php
class VerifyPassword
{
  public function verify(HasPassword $user, string $actual): bool
  {
    return \password_verify($actual, $user->getHashedPassword());
  }
}
```

これは実装が単純すぎるので参考例としては微妙ですが、ライブラリはこのようにインターフェースを経由して具象クラスを利用します。

## インターフェースの使い方はライブラリ・フレームワークを作らないとわからない

初心者にありがちなのが、 **「インターフェースの使い道がわからない」** という話です。これは、アプリケーション（＝具象）しか作っていない場合はずっと分からないかもしれません。何故なら、先ほどまで言っていた通り **「インターフェースはライブラリ・フレームワークのための機能」** だからです。

なので、一番手っ取り早くインターフェースの使い道を知る方法は **「ライブラリ・フレームワークを自作すること」** です。自作したら、嫌でも **「ここインターフェースじゃないと作れない」** という場所が出てきます。

ここで重要なのは、 **「ライブラリ・フレームワークは OSS でなければいけない、なんてことはない」** という部分です。

あなたが作っているアプリケーション、重複したコードがあったりしませんか？そうした場合、複数の場所でコードを使いまわすためにその機能を分離することがあるでしょう。 **「その分離した機能はライブラリです」** 。そう、アプリケーションの中で「ライブラリを実装する」ことは可能です。折角の機会なので、そこでインターフェースが何故使われるのか体験しましょう。

分離した機能は、要するに複数の場所から呼ばれるわけですが、呼ばれる時に欲しいデータがあったりします。それを「インターフェース」として定義して、実装側にそのインターフェースの実装を要求することで、実装に依存せず欲しいデータを取得することが出来ます。

プリミティブ型(string や int など)を要求するだけでも、「どういう値が欲しいのか」を命名をするだけで意味はあります(先ほどの例の `getHashedPassword()` のように)。

```php
class VerifyPassword
{
  public function verify(string $hashed_password, string $actual): bool
  {
    return \password_verify($actual, $hashed_password);
  }
}
```

このように書くことも出来ますが、これでは `$hashed_password` になんでも入れることが出来るので、間違えて `$raw_password` を入れてしまう可能性があります。インターフェースでメソッド化することで、より分かりやすくなるので、その間違えを減らすことが出来ます。また、 **「インターフェースは制約」** です。このライブラリを使うために **「実装しなければならないもの」** を定義しています。 `VerifyPassword` では `HasPassword` インターフェースを用いて、 **「ハッシュ化されたパスワードを保持しなければならない」制約** を利用者に課しています。

## インターフェースは制約

これがインターフェースの最も重要な機能といえます。様々な汎用機能を実装するにあたり、具象アプリケーションに必要な機能を要求する場面が出てきます。例えば、ライブラリは「ユーザー情報を永続化する具体的な方法」は分からないので、アプリケーションに実装をゆだねる必要があります。

```php
interface PersistUser
{
  public function persist(User $user): void;
}
```

ライブラリはこの機能を利用してユーザー情報を **「アプリケーションに永続化してもらいます」** 。

アプリケーションはこのインターフェースを implements し、実際に永続化するロジックを記述します。そうすることで、多様な具象アプリケーションのどれでも同じライブラリを使うことが出来ます。

**「継承よりも移譲が大事」** と最近よく耳にしますが、インターフェースはこの「移譲」を実装するために非常に便利な機能です。

コンストラクタインジェクションをしても、それが具象クラスで宣言されている場合、その具象クラスか、それを継承したクラスしかインジェクション出来ません。しかし、インターフェースにしておけば、抽象に依存することが出来るので、よりシンプルな要求ですみます。

## 過度な抽象化

このインターフェースの面白い事例として [FizzBuzzEnterpriseEdition](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition) と呼ばれる参考実装があります。

このリポジトリを見ると、数行で実装出来るはずの FizzBuzz に 25 個ものインターフェースと 61 個ものクラスが存在します。

これは **「超極端に抽象化した例」** として面白いです。 FizzBuzz という要件は非常にシンプルですが、インターフェースやデザインパターンなどを使って抽象化を極端にしていくと、複雑なプロダクトにも出来てしまうよ、という事例です。

```java
/**
 * Strategy for NoFizzNoBuzz
 */
@Service
public class NoFizzNoBuzzStrategy implements IsEvenlyDivisibleStrategy {

  /**
   * @param theInteger int
   * @return boolean
   */
  public boolean isEvenlyDivisible(final int theInteger) {
    if (!NumberIsMultipleOfAnotherNumberVerifier.numberIsMultipleOfAnotherNumber(theInteger,
        NoFizzNoBuzzStrategyConstants.NO_FIZZ_INTEGER_CONSTANT_VALUE)) {
      if (!NumberIsMultipleOfAnotherNumberVerifier.numberIsMultipleOfAnotherNumber(theInteger,
          NoFizzNoBuzzStrategyConstants.NO_BUZZ_INTEGER_CONSTANT_VALUE)) {
        return true;
      } else {
        return false;
      }
    } else if (!NumberIsMultipleOfAnotherNumberVerifier.numberIsMultipleOfAnotherNumber(theInteger,
        NoFizzNoBuzzStrategyConstants.NO_BUZZ_INTEGER_CONSTANT_VALUE)) {
      if (!NumberIsMultipleOfAnotherNumberVerifier.numberIsMultipleOfAnotherNumber(theInteger,
          NoFizzNoBuzzStrategyConstants.NO_FIZZ_INTEGER_CONSTANT_VALUE)) {
        return true;
      } else {
        return false;
      }
    } else {
      return false;
    }
  }
}
```

一部取り出してみましたが、もう何言ってるのかわかりません。命名も長い。これが **「過度な抽象化」** と呼ばれるアンチパターンです(リポジトリでも、このコードは意味がないと書かれています)。

このように、要件に対して実際いくらでもインターフェースを作ることは可能ということがわかります。なので、過度な抽象化にならないよう気を付けつつ、「アプリケーション内のライブラリ」を作ることになった時にインターフェースについて考えてみると意外な発見があるかもしれません。

## 依存性の逆転

```php
class SomeClass
{
  public function someLogic(A|B $target): void
  {
    $result = $target->solve();
    // ...
  }
}
```

インターフェースを用いず、 Union 型で複数のクラスに対応することも可能ですが、これはオススメしません。インターフェースは **「制約」** であることが重要だからです。例えば別の修正で `A` クラスの `solve` メソッドが名前を変えたりなくなったりした時、 `SomeClass` という利用者側でエラーが出ます。 `SomeClass` を知らずに `A` クラスを修正した人にとっては「なんで壊れたの？」となるでしょう。インターフェースを適切に定義していれば、 **「`A` クラスは `solve` メソッドがなければならない」** ということが、誰が見てもわかります。

また、依存性の逆転にもインターフェースは使われます。上記の例では `SomeClass` が `A` 及び `B` クラスに依存しています。

```php
interface Solvable
{
  public function solve(): string;
}

class SomeClass
{
  public function someLogic(Solvable $target): void
  {
    $result = $target->solve();
    // ...
  }
}
```

このようにインターフェース化することで、 `SomeClass` は `A` にも `B` にも依存しなくなりました。代わりに `A` 及び `B` クラスが `Solvable` インターフェースという抽象に依存します。これが **「依存性の逆転(DIP)」** です。
