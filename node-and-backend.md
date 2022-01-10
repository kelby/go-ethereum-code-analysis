`node.Node` 实例对象，称之为 stack，通过 `node.New` 构建。

`eth.Ethereum` 实例对象，称之为 backend，通过 `eth.New` 构建。

`les.LightEthereum` 实例对象，称之为 backend，通过 `les.New` 构建。

backend 是 stack 的一部分，最重要、最核心的一部分，通过 utils.RegisterEthService 注册。

除此之外，stack 还能注册 utils.RegisterGraphQLService 和 utils.RegisterEthStatsService. 它们背后分别是 ethstats.New 和 graphql.New，尽管不常用。

