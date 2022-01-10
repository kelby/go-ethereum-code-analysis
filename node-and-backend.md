`node.Node` 实例对象，称之为 stack，通过 `node.New` 构建。

`eth.Ethereum` 实例对象，称之为 backend，通过 `eth.New` 构建。

`les.LightEthereum` 实例对象，称之为 backend，通过 `les.New` 构建。

backend 只是 stack 的一部分，通过 utils.RegisterEthService 注册，除此之外，还能注册 utils.RegisterGraphQLService 和 utils.RegisterEthStatsService.



