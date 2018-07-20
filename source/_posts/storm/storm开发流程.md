---
title: storm开发流程
categories:  # 这里写的分类会自动汇集到 categories 页面上，分类可以多级
- 技术开发
tags:   # 这里写的标签会自动汇集到 tags 页面上
- 大数据
- storm
---

storm的基本开发流程，让开发者快速开始storm工程开发。

<!-- more -->

> storm开发流程

1. 引入pom依赖

```
  <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>${storm.version}</version>
        <!--<scope>provided</scope>--><!--本地模式需要将<scope>provided</scope> 屏蔽掉-->
 </dependency>

```
2. 创建spout

```
创建spout，继承BaseRichSpout
```

3. 创建bolt

```
创建bolt，继承BaseBasicBolt，此bolt不需要手动去触发ack与fail机制简化开发
```
4. 创建拓扑

```
TopologyBuilder builder = new TopologyBuilder();

// 数据源,从kafka获取（随机发送单词)
//KafkaSpout在将数据解析的时候 
builder.setSpout("参数", spout接入实例);

//内部pb转换
builder.setBolt("参数", bolt实例).shuffleGrouping("需要传输的数据");

```
5. 启动拓扑

```
 //提交拓扑
if (args !=null && args.length > 0) { //获取第一个参数做为拓扑名称
    // 集群模式
    // storm jar testCode.jar com.mapbar.storm.count.VehicleCountTopology arg1 arg2 arg3
    Config conf = new Config();
    conf.setNumWorkers(2); //配置需要多少个work来处理这个拓扑
    // 设置spout task上面有多少个没处理的tuple
    conf.setMaxSpoutPending(5000);

    try {
        StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    } catch (Exception e) {
        logger.error("提交拓扑失败!失败信息:{}", e.getMessage());
        e.printStackTrace();
    }
    logger.info("提交拓扑成功，拓扑名称:[{}]", args[0]);

} else {
    // 本地模式
    Config conf = new Config();
    // 设置task并行度
    conf.setMaxTaskParallelism(3);
    // 设置spout task上面有多少个没处理的tuple
    conf.setMaxSpoutPending(5000);

    conf.setDebug(true);

    LocalCluster cluster = new LocalCluster();
    cluster.submitTopology("拓扑名称", conf, builder.createTopology());

}
```
6. 将图谱提交到集群

```
storm jar xxxx.jar 拓扑main函数入口 拓扑名称
```
> jar打包注意

1. 需要借助maven插件将所有依赖都打入jar包
```
<build>
<finalName>test</finalName>
<plugins>
    <plugin><!--将外部依赖打入jar包-->
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
            <!--这部分可有可无,加上的话则直接生成可运行jar包-->
            <!--<archive>-->
            <!--<manifest>-->
            <!--<mainClass>${exec.mainClass}</mainClass>-->
            <!--</manifest>-->
            <!--</archive>-->
            <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
            </descriptorRefs>
        </configuration>
    </plugin>
</plugins>
</build>
```
2. 本地模式与集群模式

```
本地模式开发者进行代码调试与跟踪，集群模式代码提交给storm进行批量处理
```