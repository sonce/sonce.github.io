### 管理节点和工作节点
Docker Swarm的管理节点（Manager）默认也会作为工作节点（Worker）。这意味着管理节点不仅负责集群的管理工作，如集群配置、服务管理等，同时也会执行任务。这样的设计使得资源利用更加高效。但请注意，对于非常大的集群（例如，1000+节点），管理节点可能需要更多的资源。
### 管理节点
Docker Swarm的管理节点（Manager）采用了热备方式来提升集群的容灾能力。在Swarm的管理节点集群中，只有一个节点处于领导者（Leader）状态，负责完成Swarm节点的管理，其余的管理节点处于热备状态。

如果一个管理节点（特别是领导者节点）宕机，其余的管理节点会自动发起领导者选举，重新选举出一个新的领导者节点。这样，即使某个管理节点出现故障，Swarm集群仍能保持正常运行。因此，如果Docker Swarm的Manager节点挂了，集群会自动进行故障转移，确保服务的连续性。这也是Docker Swarm高可用性设计的一个重要特点。请注意，为了确保高可用性，建议在生产环境中配置多个管理节点。
Swarm 集群通常需要至少 3 个管理器节点。这样，即使有一个管理器节点出现故障，集群仍然可以通过投票选举出新的主管理器

管理节点(Manager)的状态：
1. Leader：这是主要的管理节点，负责做出所有群管理和编排决策。
2. Reachable：这意味着该节点是一个管理节点，正在参与Raft共识。如果当前的领导节点（Leader）变为不可用，那么这个"Reachable"状态的节点有资格被选举为新的领导节点。
3. Unavailable：这意味着该节点是一个管理节点，但不能和其他管理节点产生任何联系。在这种情况下，应该添加一个新的管理节点到集群，或者将一个工作节点（Worker）提升为管理节点

### 加入Swarm集群
将一个节点加入到一个已存在的Swarm集群中。这个过程分为两步：
1. 在管理节点上获取令牌：首先，你需要在已存在的Swarm集群的管理节点（Manager）上运行以下命令来获取一个令牌:
``` shell
docker swarm join-token worker
```
2. 在新节点上加入Swarm集群：然后，你需要在要加入Swarm集群的新节点上运行上一步获取的docker swarm join命令。
   ``` shell
   docker swarm join --token [第一步获取的TOKEN]
```
### 常用命令
1. 查看集群的所有节点：`docker node ls`
2. 查看特定节点的详细信息：`docker node inspect [主机名]`，如果想更易读的格式查看，可添加`--pretty`参数，如：`docker node inspect --pretty [主机名]`
3. 更新节点信息：`dockers node update` 如设置节点可用性为“drain”（不会在分配任务给该节点，且会停止已经存在的任务）`docker node update --availability drain`
4. 升级为管理节点：`docker node promote [主机名]`，降级为工作节点：`docker node demote [主机名]`
5. 获取节点ID：`docker info -f '{{.Swarm.NodeID}}'`
6. 部署Stack:`docker stack deploy --compose-file xxx.yaml StackName`