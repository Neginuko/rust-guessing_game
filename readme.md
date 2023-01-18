# 数字当てゲームで学ぶRustの標準入出力と基礎

アジャイル開発で学んでいくのでサイクルごとの解説

## #1

```rust
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please, input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
    
    println!("You guessed: {}", guess);
}
```

1. **useステートメント**

1行目の`use std::io;`は標準入出力を扱うための標準ライブラリをスコープに入れる宣言文
`std`という標準ライブラリにある`io`ライブラリという意味

実はデフォルトでいくつかの標準ライブラリは宣言せずともスコープに入っている
これらのライブラリは**プレリュード(prelude)**と呼ばれる
プレリュードに含まれるライブラリは[この公式ドキュメントを参照(英語)](https://doc.rust-lang.org/std/prelude/index.html)

よって、ない場合には`use`ステートメントを使ってスコープに入れてやる必要がある

2. **main関数**

エントリポイントを表す関数

3. **変数**

プレイヤーの入力を格納する変数が必要なので作る

```rust
let mut guess = String::new();
```

ちょっと難しいので簡単なもので説明

```rust
let apples = 5;
```

これは`apples`という新しい変数を作成して5という数字を束縛している
Rustはデフォルトでは不変の変数になる（再代入できない）

可変にするためには`mut`をつける

```rust
let apples = 5;         // immutable: 不変
let mut bananas = 5;    // mutable  : 可変 

```

ではさっきの説明に戻って
`guess`に束縛される値は`String::new()`で取得した値になる
この値はString型の新しいインスタンスというもので、可変長のUTF-8で書かれた文字列を表す

`::`は`new`が`String`型の**関連関数**であることを示す
関連関数とはある型に対して実装される関数という意味

まとめると、`let mut guess = String::new()`は、`guess`という変数にString型のインスタンスを束縛する
という意味になる

4. **標準入力**

```rust
io::stdin()
    .read_line(&mut guess)
```

ここでは、プレイヤーからの入力を得る処理を行なっている
`io::stdin()`は、最初の説明で行なった`use`を使わない場合には`std::io::stdin()`のように呼び出せる

`io::stdin()`は標準入力のハンドルを行う型のインスタンスを返す

次の`read_line(&mut guess)`はプレイヤーからの入力を得る処理を行なっている
引数に`&mut guess`のような形を指定することで、受け取った入力を渡した変数へ代入を行う


5. **失敗の可能性を扱う**

`io::stdin()`はプレイヤーの入力を代入するともに、`io::Result`と呼ばれる実行結果を表す型を返す
`.expect()`はその`io::Result`で失敗したという結果が返ってきた時に処理を行う
そのため、処理が失敗した時の処理を扱うために、この`.expect()`が必要なのである

この処理を省略することはできず、ない状態でコンパイルを行うと失敗する


## #2

```rust:main.rs
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    println!("Please, input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
    
    println!("You guessed: {}", guess);
}
```

```toml:Cargo.toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
rand="0.8.3"
```


## #3

```rust:main.rs
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    println!("Please, input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
    
    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

1. **match式**

パターンマッチングを行う式
Switch式に近い


# #4

```rust
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    println!("Please, input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
    
    let guess: u32 = guess.trim().parse()
        .expect("Please input number!");
    
    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```


# #5

```rust:main.rs
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    loop {        
        println!("Please, input your guess.");
        
        let mut guess = String::new();
        
        io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
        
        let guess: u32 = guess.trim().parse()
        .expect("Please input number!");
        
        println!("You guessed: {}", guess);
        
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {println!("You win!")},
        }
    }
}
```

# #6

```rust:main.rs
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    loop {        
        println!("Please, input your guess.");
        
        let mut guess = String::new();
        
        io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
        
        let guess: u32 = guess.trim().parse()
        .expect("Please input number!");
        
        println!("You guessed: {}", guess);
        
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

# #7

```rust:main.rs
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("secret number is {}", secret_number);

    loop {        
        println!("Please, input your guess.");
        
        let mut guess = String::new();
        
        io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
        
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue;
        };
        
        println!("You guessed: {}", guess);
        
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

# #8 release

```rust:main.rs
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {        
        println!("Please, input your guess.");
        
        let mut guess = String::new();
        
        io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line.");
        
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
        
        println!("You guessed: {}", guess);
        
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```