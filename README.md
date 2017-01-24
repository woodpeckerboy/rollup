# Rollup
Rollup 希望提供一个去中心化的「抽奖」系统 —— 在抽奖的过程中没有人有特权，一旦有人作弊会被所有人发现。

传统的抽奖系统都是运行在单机上，那么这台设备就可能被做过手脚，抽奖的结果也不能令所有人信服。因此 Rollup 会以分布式的方式运行在所有人的设备上，会有一个后端服务器帮助客户端进行广播，但没有任何特权，每个客户端都会对抽奖过程进行验算，一旦有人作弊就会被发现。

Rollup 只保证在成功选出中奖者时无人作弊，如果有人作弊那么抽奖过程会被中断，如果有人执意捣乱的话会导致抽奖一直无法完成。同时 Rollup 也不会对参与抽奖的资格进行验证，需要在下文提到的「确认参与者」的步骤中自行进行验证。

## 算法

Rollup 基于两阶段提交来实现，在第一阶段：

- 每个人本地生成一个随机数（记作 `secretNumber`）和一个 `hashSalt`（用于防范彩虹表），将 `secretNumber` 和 `hashSalt` 散列之后（记作 `numberHash`）连同自己的名字一起广播给其他人。
- 在第一阶段每个人都会收到他人发来的广播，大家需要把收到的广播记录下来，并检查是否所有人都正确地进行了广播，确认所有参与者都进行了广播（否则等待剩余参与者广播 `numberHash`），确认没有人用不同的名字进行多次广播、没有不应参加的名字（否则抽奖中断）。
- 每个人在进行确认后开始广播 `hashSalt`（广播 `hashSalt` 表示参与者名单确认完成），同时也会收到其他人广播的 `hashSalt`

对于每个人而言，当收到所有参与者的 `hashSalt` 之后，进入第二阶段：

- 每个人开始广播自己的 `secretNumber`，并接受了记录其他人的 `secretNumber`（也需要使用之前的 `hashSalt` 和 `numberHash` 进行验算）。
- 在收到第一阶段所有参与者的 `secretNumber` 之后，将 `secretNumber` 排序、组合并进行散列（记作 `luckyNumber`）。
- 对比每个人的 `secretNumber` 与 `luckyNumber` 的差值，按照差值排序，即为抽奖的结果。

这个算法的要点就在于，在第一阶段中，每个人都选定了一个随机数；而在第二阶段中，大家广播这个随机数并一起生成一个 luckyNumber，决定中奖者名单。之所以需要在第一阶段广播这个随机数的散列值，就是为了保证不会有人在看到其他人的随机数之后，有意地选择自己的数字，来控制最后的 luckyNumber.

## 讨论

> 如果有人执意捣乱会怎样？

TODO

> 如果后端没有公正地进行广播会怎样？

TODO
