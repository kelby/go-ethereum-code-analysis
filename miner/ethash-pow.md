ethash的大概流程是这样的：

1.先根据block number以及block header计算出一个种子值seed



2.使用seed产生一个16MB的伪随机数集cache



3.根据cache生成一个1GB的数据集DAG\(可以根据cache快速定位DAG中指定位置的元素，所以一般轻客户端保存cache，完整客户端保存DAG\)



4.从DAG中随机选择元素对其进行hash，然后判断哈希值是否小于给定值



5.cache和DAG每个周期\(1000个块\)更新一次。DAG从1GB开始随着时间线性增长，现在好像达到20多GB了





