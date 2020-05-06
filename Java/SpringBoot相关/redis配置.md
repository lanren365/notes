# 1. 整合Redis的几种方式
## 1.1 不指定redis连接池
### 1.1.1 配置
```yml
spring:
  redis:
    cluster:
      nodes:
        - 192.168.1.236:7001
        - 192.168.1.236:7002
        - 192.168.1.236:7003
        - 192.168.1.244:7004
        - 192.168.1.244:7005
        - 192.168.1.244:7006
      max-redirects: 3  # 获取失败 最大重定向次数
    pool:
      max-active: 1000  # 连接池最大连接数（使用负值表示没有限制）
      max-idle: 10    # 连接池中的最大空闲连接
      max-wait: -1   # 连接池最大阻塞等待时间（使用负值表示没有限制）
      min-idle:  5     # 连接池中的最小空闲连接
    timeout: 6000  # 连接超时时长（毫秒）
```
### 1.1.2 使用
这种方式 redisTemplate 可直接使用默认，在使用的地方直接注入即可
```java
 @Autowired
 private RedisTemplate<String, Object> redisTemplate;
 ```
 
## 1.2. 使用jedis连接池
### 1.2.1. 配置
#### 1.2.1.1. 配置文件配置
```yml
spring:
  redis:
    password:    # 密码（默认为空）
    timeout: 6000ms  # 连接超时时长（毫秒）
    cluster:
      nodes:
        - 192.168.1.236:7001
        - 192.168.1.236:7002
        - 192.168.1.236:7003
        - 192.168.1.244:7004
        - 192.168.1.244:7005
        - 192.168.1.244:7006 
    jedis:
      pool:
        max-active: 1000  # 连接池最大连接数（使用负值表示没有限制）
        max-wait: -1ms      # 连接池最大阻塞等待时间（使用负值表示没有限制）
        max-idle: 10      # 连接池中的最大空闲连接
        min-idle: 5       # 连接池中的最小空闲连接
```
#### 1.2.1.2. 代码配置

```java
//连接池注入配置信息

@Configuration
public class RedisConfig {
   @Autowired
   private RedisConnectionFactory factory;

   @Bean
    public RedisTemplate<String, Object> redisTemplate() {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(factory);
        return redisTemplate;
    }
}
```

### 1.2.2 使用
在使用的地方直接注入即可

```java
@Autowired
 private RedisTemplate<String, Object> redisTemplate;
 ```

## 1.3. 使用lettuce连接池（推荐）
### 1.3.1. 配置
#### 1.3.1.1 配置文件配置
``` yml
spring:
  redis:
    timeout: 6000ms
    password: 
    cluster:
      max-redirects: 3  # 获取失败 最大重定向次数 
      nodes:
        - 192.168.1.236:7001
        - 192.168.1.236:7002
        - 192.168.1.236:7003
        - 192.168.1.244:7004
        - 192.168.1.244:7005
        - 192.168.1.244:7006 
    lettuce:
      pool:
        max-active: 1000  #连接池最大连接数（使用负值表示没有限制）
        max-idle: 10 # 连接池中的最大空闲连接
        min-idle: 5 # 连接池中的最小空闲连接
        max-wait: -1 # 连接池最大阻塞等待时间（使用负值表示没有限制）
```

#### 1.3.1.2 代码配置
```java
//连接池注入配置信息

@Configuration
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisConfig {    
    @Bean
    public RedisTemplate<String, Object> redisCacheTemplate(LettuceConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```
### 1.3.2 使用
在使用的地方直接注入即可
```java
 @Autowired
 private RedisTemplate<String, Object> redisTemplate;
 ```