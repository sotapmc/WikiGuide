# 刷新逻辑

MissionTap 内的任务刷新是由一个每秒执行一次的 `BukkitRunnable` 对象所实现的（不大影响服务器性能），每一次刷新时都会向 `/generated/meta.yml` 中写入本次刷新时间和下次刷新时间，当判断现在的时间晚于下次刷新时间，代表需要进行刷新，刷新流程则会开始。

刷新流程主要分为三个部分

1. 清空所有玩家的所有过期任务
2. 刷新每个玩家的任务池
3. 如果 `require-acceptance` 被设置为 `false`，则自动帮玩家接受所有任务

## 清空所有玩家的所有过期任务

每个玩家在接受任务的时候，存储该玩家数据的文件中将会生成一份接受信息。

?> **解释「接受信息」**<br>接受信息是玩家在每接受一个新任务时所生成的用来记录玩家完成任务和该任务其它基本信息（例如过期时间等）的数据结构。一个接受信息的定位由三个参数实现，分别是该玩家的 UUID、任务的类型、任务的独特键（unique key）。任何时候，只要得知这三者，便可以定位一个接受信息。

接受信息中一般会带有该任务的接受时间、过期时间和玩家完成该任务的数据。如果当前时间晚于或恰好为过期时间，代表该任务已经过期，将会被清除。每次刷新的时候，都会按照这个逻辑对玩家存在的过期任务进行处理。

### 拖延症模式的实现

接受信息生成时，会获取一个过期时间。通常情况下，这个过期时间就是下一次刷新的时间，存储在 `/generated/meta.yml` 中。而由于拖延症模式的特殊性，导致有些周任务未必要遵循在下一次刷新的时候过期，而是在接受任务的一个月后刷新。这样，只需要在接收信息生成时，将过期时间修改为**当前时间加上一个月**的结果即可。

每一次刷新时都会对过期的任务进行清理，由于拖延症模式下写入的周任务的过期时间被刻意延长，所以清理的时候自然会跳过这些周任务。只要不手动将周任务从主任务池中清除或者修改，这个逻辑就不会出错。

## 刷新每个玩家的任务池

每次刷新都会重新从主任务池中随机抽取任务并放入到玩家的任务池中，这是刷新的核心操作。该过程同时也会更新 `/generated/meta.yml`。

## 如果 `require-acceptance` 被设置为 `false`，则自动帮玩家接受所有任务

`require-acceptance` 被设置为 `false` 时代表不需要玩家手动接受任务。这种情况下，每次刷新时都会将刷新后的新任务再自动提交到玩家的「当前进行中」列表中，相当于是自动帮玩家接了任务。

?> **此处的待实现逻辑**<br>为了避免每次刷新时出现与上次相同的任务，*可能*在不远的将来将会推出对玩家未完成且未过期的任务的判断，若存在，则新生成的任务列表中不会出现这些任务。如果玩家把主任务池的所有任务都揽在手里不做，将导致刷不出新任务。

## 关于刷新的主体

刷新操作的主体是位于 `/generated/player/` 下名为各种 UUID 的文件。这些文件里面存储了每个玩家对应的任务池。每次刷新时都会对这些任务池进行更新，这样以来，玩家便可以看到新的任务，且玩家与玩家之间的任务也不完全相同。但此时就有一个问题：当有些玩家第一次进入服务器，或者是这个插件安装之后玩家第一次进入服务器，这些文件本身不存在，该怎么处理？

实际上，在每次玩家进入服务器的时候，都会对该目录下的文件进行检测。如果缺少当前玩家的文件，那么将会自动生成一份，走一遍上面的逻辑 2、3。走完逻辑 2、3 后，一个完整的玩家任务池就有了，到下一次刷新时和刷新逻辑一起走即可。