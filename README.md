# 华为2019软件精英挑战赛--初赛33名
### <a href = "https://codecraft.huawei.com/Generaldetail">赛题介绍</a>
### 队伍名称：TestNWPU
### 队员:高轶群，曹悦
### 整体思路
### 1.车辆路径选择问题
		我们首先考虑所有车辆走最短的路径，然后动态的调整车辆的出发时间来降低整体的调度时间，但是在实际实验中发现车辆走最短路径存在着死锁产生概率高和道路拥挤程度不均衡的问题，所以我们在路径选择方面做了车辆的实时调度，具体思路是当车辆在一个路口决定下一次行驶的方向的时候，会计算选择不同道路的剩余距离和该道路的拥挤程度，拥挤程度（road_full）用当前道路上的车辆数目除以道路理论能容纳的最大车辆数目来衡量，0代表道路上没有任何车，1代表道路上塞满了车，然后用road_full来惩罚距离，最终比较Dist×（1+road_full）的大小来实时选择需要的路径，Dist为选择该路口需要行驶的剩余距离。调度器集成在代码中，线上线下总调度时间结果一致。  
### 2.死锁的解除
		车辆行驶的时候可能会发生死锁形成环路，我们认为死锁是因为部分道路上车辆数目过多导致的，并且速度慢的车时导致死锁的关键。如果在T时间发生死锁，就找出来发生死锁的车，然后先把发生死锁的车按照车速排序，然后取速度最慢的一半车辆将他们的发车时间推迟到T，对于已经上路的车（除了发生死锁的车），我们不改变他们的发车时间，对于在T时刻还没有上路的车，我们会事先计算他们可能会经过的路段，如果会经过死锁路段，我们也将这些车推迟到T时间以后发车。实验证明通过这样的手段，能很好的解除死锁。  
### 3.发车策略
		对于车辆的发车，我们主要考虑到车速，车辆快的车应该具有更大的优先权，因为如果你先发速度慢的，再发速度快的车，肯定没有先发速度快的，再发速度慢的车效果好。在每一个时间片T，我们只发速度最快的车，通过这样的策略我们初赛的分数提高了100分，但是也不能无限制的发车，所以我们去限制道路上最大的车辆数目，只有小于这个数目的时候，才会从车库发车，注意到我们不用担心死锁，因为我们时可以解除死锁的，只是如果你放入更多的车，死锁的概率就会更大，就需要更多的时间去解锁。  
### 4.反思
		我觉得我们最大的败笔在发车策略上，通过限制路上车的数目来控制发车是不恰当的行为，事实也证明我们过分的拟合了A榜，在数据更换时成绩出现了较大的下滑。这个比赛对我来说已经结束了，祝大家取得好成绩。  
