# 第6章　クラスの一歩進んだ使い方

## 6-1　オーバーロードとthis

### コンストラクタとメソッドのオーバーロード

コンストラクタやメソッドは引数を変更することで、複数の種類を作ることができる。実行は、引数の型や数によって判定される。これをオーバーロードと呼ぶ。

```java
class Point8 {
    int x;
    int y;

    // 座標値を変更する。xとyの値は引数で指定する
    void set(int x, int y) {
        this.x = x;
        this.y = y;
    }

    // 座標値を変更する。xとyは、引数で渡されるインスタンスの値と同じにする。
    void set(Point8 p) {
        this.x = p.x;
        this.y = p.y;
    }

    Point8() {
        this(0,0); // thisを自身の中で使えば省略して書ける。ただし、コンストラクタの先頭行でしか利用できない
        System.out.println("引数のないコンストラクタが実行されました");
//        this.x = 0;
//        this.y = 0;
    }
    Point8(Point8 p) {
        this(p.x, p.y);
        System.out.println("引数が1つのコンストラクタが実行されました");
//        this.x = p.x;
//        this.y = p.y;
    }
    Point8(int tx, int ty) {
        System.out.println("引数が2つのコンストラクタが実行されました");
        x = tx;
        y = ty;
    }
}

class OverloadExample {
    public static void main(String[] args) {
        Point8 p1 = new Point8();
        System.out.println("p1の座標は" + p1.x + "," + p1.y);
        p1.set(10, 0);
        System.out.println("p1の座標は" + p1.x + "," + p1.y);

        Point8 p2 = new Point8();
        p2.set(p1);
        System.out.println("p2の座標は" + p2.x + "," + p2.y);

        Point8 p3 = new Point8();
        System.out.println("p1の座標は" + p3.x + "," + p3.y);

        Point8 p4 = new Point8(p3);
        System.out.println("p1の座標は" + p4.x + "," + p4.y);

        Point8 p5 = new Point8(5, 5);
        System.out.println("p1の座標は" + p5.x + "," + p5.y);
    }
}
```

### メソッドとコンストラクタを呼び出すためのthis

コンストラクタの中でthisを使用して、自分自身を呼び出すことで省略できる。ただし、コンストラクタの先頭行に限る。

```java
    Point8() {
        this(0,0); // thisを自身の中で使えば省略して書ける。ただし、コンストラクタの先頭行でしか利用できない
        System.out.println("引数のないコンストラクタが実行されました");
//        this.x = 0;
//        this.y = 0;
    }
    Point8(Point8 p) {
        this(p.x, p.y);
        System.out.println("引数が1つのコンストラクタが実行されました");
//        this.x = p.x;
//        this.y = p.y;
    }
```

### thisキーワードの省略

メソッド中で参照される変数が、インスタンス変数であることが明らかな場合、thisキーワードは省略できる。

ただし、引数名とインスンタンス変数の名前が同じである場合は、両者を区別できないため、省略できない

```java
// 引数名とインスタンス変数が異なる名前であれば、thisを省略できる
    Point8(int tx, int ty) {
        System.out.println("引数が2つのコンストラクタが実行されました");
        x = tx;
        y = ty;
    }
```

## 6-2　クラス変数とクラスメソッド

### クラス変数

クラスのフィールドでは、以下の2種類の変数を宣言できる。

- インスタンス変数：インスタンスが持つ変数
- クラス変数：クラスに関する情報、クラスから生成されたすべてのインスタンスに関する情報、あるいはインスタンスの間で共有したい値を扱うために使われる
  - クラス変数は「インスタンスを生成しなくても使用できる」という特徴がある

  クラス変数は``static 型 クラス変数名``で宣言できる。

```java
// クラス変数counterを宣言する。クラス変数の初期化はフィールドでクラス変数を宣言したときに同時に行う
class Point9 {
    static int counter = 0; // クラス変数
    int x;
    int y;

    Point9(int x, int y) {
        System.out.println("Pointクラスのコンストラクタが呼び出されました");
        this.x = x;
        this.y = y;
        counter++; // Point9クラスであることは明らかなので、counter++として省略できる。省略記法で慣れるのがよい。
    }
}
```

### クラスメソッド

クラスメソッドは、インスタンスを生成しなくても呼び出すことができる。ただし、クラスメソッドの中から、インスンタンスメソッドやインスタンス変数の、参照/呼び出しはできない。

クラスメソッドは``static 戻り値 クラスメソッド名(引数){ 命令文 }``で宣言できる。

### クラスの構造の復習

- クラスの構造
  - インスタンス変数：インスタンスで利用できる変数。
  - クラス変数：インスタンス全体で共有される。クラスそのものの情報としても利用できる。
  - コンストラクタ：インスタンスが生成されるときに自動的に実行されるメソッド。オーバーライドできる。
  - インスタンスメソッド：インスタンスの機能を定義したもの。オーバーライドできる。
  - クラスメソッド：クラスに備わったメソッド。インスタンスがなくても使える

すべていくつでも作成することができる。

## 6-3　クラスの一歩進んだ使い方の実例（顧客カードクラスの改良）

### 「次のID番号」をクラス変数で管理

クラス変数を使ってnextIdを宣言し、コンストラクタの実行時に1ずつ更新されていくようにする

```java
class CustomerCard {
    static int nextId = 1001;

    int id;
    String name;
    String address;
    double shoeSize;

    CustomerCard(String name, String address, double shoeSize) {
        this.id = CustomerCard.nextId;
        CustomerCard.nextId++;
        this.name = name;
        this.address = address;
        this.shoeSize = shoeSize;
    }

    // 名前だけで登録する
    CustomerCard(String name) {
        this(name,"",0.0);
    }
```

### とりあえず氏名だけを登録できるコンストラクタ

コンストラクタをオーバーライドすることで、名前だけを引数にとったときのコンストラクタを作る。重複した部分(これにはidの更新も含まれている)はthisで省略し、引数にname、他は適当な値にする。

```java
    CustomerCard(String name, String address, double shoeSize) {
        this.id = CustomerCard.nextId;
        CustomerCard.nextId++;
        this.name = name;
        this.address = address;
        this.shoeSize = shoeSize;
    }

    // 名前だけで登録する
    CustomerCard(String name) {
        this(name,"",0.0);
    }
```

### 改良されたプログラムコードと実行結果

クラス変数でidを管理したことで、IDの入力ミスが起こらないようになった。また、コンストラクタをオーバーライドし、とりあえず名前だけで登録できるようになった。

```java
class CustomerCard {
    static int nextId = 1001;

    int id;
    String name;
    String address;
    double shoeSize;

    CustomerCard(String name, String address, double shoeSize) {
        this.id = CustomerCard.nextId;
        CustomerCard.nextId++;
        this.name = name;
        this.address = address;
        this.shoeSize = shoeSize;
    }

    // 名前だけで登録する
    CustomerCard(String name) {
        this(name,"",0.0);
    }

    void printInfo() {
        System.out.println("ID：" + this.id);
        System.out.println("氏名：" + this.name);
        System.out.println("住所：" + this.address);
        System.out.println("靴のサイズ：" + this.shoeSize);
    }
}

class CustomerManager {
    public static void main(String[] args) {
        CustomerCard[] cards = new CustomerCard[100];
        cards[0] = new CustomerCard("山田太郎", "東京都", 26.5);
//        cards[1] = new CustomerCard("佐藤花子", "千葉県", 24.5);
        cards[1] = new CustomerCard("佐藤花子");
        cards[2] = new CustomerCard("鈴木健児", "茨城県", 26.0);


        for(int i = 0; i < 100; i++ ) {
            if(cards[i] == null) {
                break;
            }
            System.out.println(i + "番目の顧客カードに記載の情報");
            cards[i].printInfo();
            System.out.println("=================");
        }
    }
}
```

### この章で学んだこと

```
コンストラクタは引数を変えて複数つくることができる。これをオーバーロードと呼び、オーバーロードを使えば、異なる引数でインスタンスを生成することができる。なお、オーバーロードは、メソッドも同様に行うことができる。

コンストラクタの中でthisを用いて、自分自身を呼び出すことで、省略して書くことができる。コンストラクタが複数あれば、重複しているところをさらに書かなくてもよくなる。

クラスが明らかであればメソッドでもコンストラクタでも、thisを省略して書くことができる。大抵の場合は省略されるため、省略記法に慣れた方がよい。

クラス変数、クラスメソッドは、staticをつけて作成する。どちらもインスタンスを生成しなくても使用することができるが、インスタンス変数やインスタンスメソッドを用いることも参照することもできない。そのクラス全体で共有しておきたい変数やメソッドがあるときに役に立つ。
```