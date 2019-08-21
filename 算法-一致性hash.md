```
    1.可参考 https://www.cnblogs.com/xrq730/p/5186728.html
    java版本的一致性hash
    2.算法用于将大量数据分配到几个节点上，可以保证同一个数据每次会分配到同一个节点，
    且负载均衡，增加新数据不影响原本数据的分配位置；
    3.算法会使用虚拟节点来提高均衡程度；
    4.算法基本过程：先构造一个长度为232的整数环（这个环被称为一致性Hash环），根据节点
    名称的Hash值（其分布为[0, 232-1]）将服务器节点放置在这个Hash环上，然后根据数据的Key值
    计算得到其Hash值（其分布也为[0, 232-1]），接着在Hash环上顺时针查找距离这个Key值的Hash值
    最近的服务器节点，完成Key到服务器的映射查找。
```
```
    场景:
    有大量topic，多个kafka节点，需要每个节点采集部分topic的数据，每个节点采集量均衡，
    注意：会有新增topic情况出现，无法使用排序解决；
```
```
    java实现:

    private int brokerId;
    private List<Integer> brokerList = new ArrayList<>();
    private TreeMap<Integer, String> sortedMap = new TreeMap<>();

    public SplitListWorker(AdminClient adminClient, Node anyNode, String ip){
        this.findAllBrokers(adminClient, anyNode, ip);
        initVNodeHash();
    }

    private void findAllBrokers(xx, xx, xx){
        ... //获取所有节点，类似(1,2,3,4,5)
    }

    //输入一个全量topic列表，返回分配到本broker的topic列表
    public List<String> splitTopicList(List<String> topicList){
        List<String> result = new ArrayList<>();
        for(String topic : topicList){
            if(getNode(topic).equals(String.valueOf(brokerId))) result.add(topic);
        }
        return result;
    }

    //需要判断的topic，获取到顺时针第一个虚拟节点，虚拟节点对应的节点就是这个topic分配到的节点
    private String getNode(String key){
        SortedMap<Integer, String> subMap = sortedMap.tailMap(getHash(key));
        String vNode = subMap.get(subMap.firstKey());
        return vNode.split("@")[0];
    }

    private void initVNodeHash(){
        int vNodeNum = countVNodeNum(brokerList.size());
        for (int broker : brokerList){
            for (int i = 0; i < vNodeNum; i++){
                String vNodeStr = broker + "@" + String.valueOf(i);
                sortedMap.put(getHash(vNodeStr), vNodeStr);
            }
        }
    }

//    FNV1_32_HASH
    private int getHash(String str){
        final int p = 16777619;
        int hash = (int)2166136261L;
        for (int i = 0; i < str.length(); i++) {
            hash = (hash ^ str.charAt(i)) * p;
            hash += hash << 13;
            hash ^= hash >> 7;
            hash += hash << 3;
            hash ^= hash >> 17;
            hash += hash << 5;
        }
        if (hash < 0) hash = Math.abs(hash);
        return hash;
    }

    private int countVNodeNum(int brokerCount){
        int value;
        if(brokerCount > 40) value = 5;
        else if(brokerCount > 20) value = 20;
        else if(brokerCount > 5) value = 50;
        else value = 100;
        return value;
    }
```
