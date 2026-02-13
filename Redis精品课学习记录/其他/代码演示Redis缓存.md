# 架构图
![[Pasted image 20250930214838.png]]

> 分层:
> 1. controller
> 2. service
> 3. dao

# controller
```java
package org.example.l20250930.demos.controller;  
  
import org.example.l20250930.demos.service.CacheService;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.ResponseBody;  
  
/**  
 * 控制层对象  
 */  
@Controller  
@ResponseBody  
@RequestMapping("/data")  
public class CacheController {  
    private final CacheService cacheService;  
  
    public CacheController(CacheService cacheService) {  
        this.cacheService = cacheService;  
    }  
  
    @RequestMapping("/getData")  
    public String getData(String key) {  
        if(key == null) return "参数不能不空!";  
        return cacheService.getData(key);  
    }  
}
```

# service
```java
package org.example.l20250930.demos.service;  
  
import org.example.l20250930.demos.dao.CacheDataMapper;  
import org.example.l20250930.demos.entity.CachedData;  
import org.springframework.data.redis.core.StringRedisTemplate;  
import org.springframework.stereotype.Service;  
  
import java.util.concurrent.TimeUnit;  
  
/**  
 * 业务层  
 */  
@Service  
public class CacheService {  
    private final StringRedisTemplate redisTemplate;  
    private final CacheDataMapper cacheDataMapper;  
  
    public CacheService(StringRedisTemplate redisTemplate, CacheDataMapper cacheDataMapper) {  
        this.redisTemplate = redisTemplate;  
        this.cacheDataMapper = cacheDataMapper;  
    }  
  
    public String getData(String key) {  
        // 1.先从Redis中查询  
        String cachedValue = redisTemplate.opsForValue().get(key);  
        if(cachedValue != null) {  
            System.out.println("Redis中存在要查询的数据, 缓存命中");  
            return cachedValue;  
        }  
  
        // 2.从MySQL查询  
        System.out.println("缓存未命中 miss --> 接下来从数据库查询");  
        CachedData cachedDataInDB = cacheDataMapper.findByKey(key);  
        if(cachedDataInDB != null) {  
            System.out.println("数据库中成功查询, 先写入Redis");  
            redisTemplate.opsForValue().set(key, cachedDataInDB.getValue(), 1, TimeUnit.HOURS);  
            return cachedDataInDB.getValue();  
        }  
        return "404 数据不存在";  
    }  
}
```

# dao
```java
package org.example.l20250930.demos.dao;  
  
import org.apache.ibatis.annotations.Mapper;  
import org.apache.ibatis.annotations.Select;  
import org.example.l20250930.demos.entity.CachedData;  
  
/**  
 * 数据层 : 从数据库获取数据  
 */  
@Mapper  
public interface CacheDataMapper {  
    @Select("SELECT id, cache_key, value FROM cache_data WHERE cache_key = #{key}")  
    CachedData findByKey(String key);  
}
```

# entity
```java
package org.example.l20250930.demos.entity;  
  
import lombok.Data;  
  
/**  
 * 存储数据的实体  
 */  
@Data  
public class CachedData {  
    private int id;  
    private String key;  
    private String value;  
}
```

>预期结果 : 第一次查询时由于在Redis中没有存储相应的数据,缓存miss,从MySQL中获取数据并写入Redis
>第二次查询直接从Redis获取数据

**第一次查询**
![[Pasted image 20250930225854.png]]
![[Pasted image 20250930225922.png]]

在Redis中验证是否执行写入缓存
![[Pasted image 20250930230423.png]]
**第二次查询**
![[Pasted image 20250930225950.png]]