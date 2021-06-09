# 1. 查询
## 1.1. 单表查询
### 1.1.1. 全量查询语句
```java
Criteria cri =new Criteria();
Query query=new Query();
query.addCriteria(cri);
MongoTemplate().find(query, Model.class);
```
### 1.1.2. 带条件查询
#### 1.1.2.1. 固定条件数
```java
Criteria cri =new Criteria();
Criteria[] cri1 = {Criteria.where("score").lte(max),Criteria.where("creditScored").gte(min)};//连在一起写：Criteria.where("score").lte(max).gte(min)
cri.andOperator(cri1);
Query query=new Query();
query.addCriteria(cri);
MongoTemplate().find(query, Model.class);
```
#### 1.1.2.2. 非固定条件数
```java
List<Criteria> condition=new LinkedList<Criteria>();
if(!StringUtils.isEmpty(id)){
	condition.add(Criteria.where("id").is(id));
}
if(!StringUtils.isEmpty(inputsName)){
	condition.add(Criteria.where("inputName").regex(inputsName));
}
condition.add(Criteria.where("isDelete").is(1));
Criteria[] cri=condition.toArray(new Criteria[condition.size()]);
Query query=new Query();
query.addCriteria(cri);
MongoTemplate().find(query, Model.class);
```
#### 1.1.2.3. 或者条件
```java
//该方法不是Criteria的静态方法，必须使用对象实例调用
Criteria criatira = new Criteria()
criteria.orOperator(criteriaA,criteriaB,criteriaC,criteriaD);
//第二种
new Criteria().orOperator(Criteria.where("updateStatus").is(-1),
			Criteria.where("updateStatus").is(2))};

//错误写法： 一个查询语句中，不能出现两个criteria.andOperator()方法 ，or方法也一样
criteria.andOperator(criteriaA,criteriaB);
criteria.andOperator(criteriaC,criteriaD);
```
#### 1.1.2.4. 根据条件查询，并且返回指定列，加入排序
```java
public List<T> findSomeColumn(Map<String, Object> conditions,Map<String, Object> columns,
		String sortStr,int how) throws Exception{
	//BasicDBObject是BSON的子类，方法更加完善
	//查询条件
	BasicDBObject condition=new BasicDBObject();
	for(Map.Entry<String, Object> entry : conditions.entrySet()) {  
		condition.append(entry.getKey(),entry.getValue());  
	}  
	//指定需要显示列  
	BasicDBObject key=new BasicDBObject();
	for(Map.Entry<String, Object> entry : columns.entrySet()) {  
		key.append(entry.getKey(),entry.getValue());  
	}
	Query query=new BasicQuery(condition,key);
	
	//根据某个字段排序，如果how=1就是desc，how=2就是asc
	if(how==1){
		query.with(new Sort(new Order(Direction.DESC,sortStr)));
	}else{
		query.with(new Sort(new Order(Direction.ASC,sortStr)));
	}
	
	return getMongoTemplate().find(query, clazz);
}
```
### 1.1.3. 分组查询
```java
public JSONArray GroupByTempName(String enterpriseId) throws Exception {
		// TODO Auto-generated method stub
		//加入查询筛选条件
		BasicDBObject matherTimer=new BasicDBObject("enterpriseObj.$id",new ObjectId(enterpriseId)); //因为enterpriseObj对象加了@DBRef标签
		//多个条件
//		DBObject queryObject2 = new BasicDBObject();
//		BasicDBObject queryObject1=new BasicDBObject("uploadTime", new BasicDBObject("$gte",startTime+" 00:00:00").append("$lte",endTime+" 23:59:59"));  
//        if (!StringUtils.isEmpty(enterpriseId)) {
//        	queryObject2 = queryObject1.append("enterpriseId", enterpriseId);
//		}else {
//			queryObject2 = queryObject1;
//		}
		DBObject matcher1=new BasicDBObject("$match",matherTimer);
		
		//加入分组条件
		BasicDBObject groupId=new BasicDBObject();
		groupId.append("tempName","$tempName");  //分组字段  $tempName表示数据库字段   tempName字段为自定义名称字段，可以加入多组分组依据
		DBObject groupEnterpriseId=new BasicDBObject("_id",groupId); //_id为固定命名，不可更改
		//groupEnterpriseId.put("total", new BasicDBObject("$sum","$productAmount"));  //分组统计数据库字段$productAmount的和
		//groupEnterpriseId.put("batchTotal", new BasicDBObject("$sum",1));   //统计该组数据的总数，batchTotal为自定义字段名
	    DBObject  group1=new BasicDBObject("$group",groupEnterpriseId);
	    
	    //加入排序字段
//	    BasicDBObject sortSimpleProductTotal=new BasicDBObject();
//	    sortSimpleProductTotal.append("$sort",new BasicDBObject("_id.total",-1));  // _id指的是上面的id，不是数据库主键。  total是上面查询出来的字段
	    
	    //加入查询条数
//	    BasicDBObject limitNum =new BasicDBObject("$limit",20);
	    
	    List<DBObject>  list=(List<DBObject>)getMongoTemplate().getCollection("templateInfoData").aggregate(matcher1,group1).results();
	    //如果加了排序字段和查询条数，则使用下面的语句
	    //List<DBObject>  list=(List<DBObject>)getMongoTemplate().getCollection("batch").aggregate(matcher1,group1,sortSimpleProductTotal,limitNum).results();
	    JSONArray dataArray = new JSONArray();
	    for(DBObject obj:list){
	    	dataArray.add(obj);
	    }
	    return dataArray;
		 
	}
```
## 1.2. 多表联合查询
