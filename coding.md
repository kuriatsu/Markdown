coding memo
===

## memory確保と効率

+ push_back などで要素を1つずつ挿入していきたい場合は`reserve`関数
+ 添字アクセスなどで任意の位置に要素を代入したい場合は`resize`関数 or `vector(size_type size)`コンストラクタ
+ 特定の値を敷き詰めたい場合は`resize` + `fill` or `vector(size_type num, const TYPE &val)` コンストラクタ
