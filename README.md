steam4j
=======
Steam4j is a Java based 3rd party client library for accessing the Steam API.

##Features
 - Clean, simple data access layer and data objects
 - Access to underlying API data for ultimate control
 - Threadsafe Spring-like template-based data access objects.
 - Built in schema caching based on 'last-modified' headers. Forced refresh available.
 - Ultra fast serialisation via Jackson - 700 nanoseconds per 2000 slot backpack.
 - Simplified access to the most common item attributes.
 - Compatible with services like mockable.io for easy testing.
 - Built in retry logic (switched off by default)
 - Limiter capabilities to comply with Steam's 100,000 calls per day limit (unlimited by default)

##Quickstart

###Maven
```xml
<dependency>
    <groupId>uk.co.solong</groupId>
    <artifactId>steam4j</artifactId>
    <version>2.0.17</version>
</dependency>
```

Collections: (only required to make use of Limiter capabilities)
```xml
<dependency>
    <groupId>uk.co.solong</groupId>
    <artifactId>collections</artifactId>
    <version>0.0.2</version>
</dependency>
```
 
###Retrieving a backpack
```java
TF2Template dao = new TF2Template("API_KEY_HERE");
TF2Backpack backpack = dao.getPlayerItems(76561197971384027L);
if (Status.SUCCESS.equals(backpack.getStatus())){
    for (TF2Item item: backpack.getItems()) {
        System.out.println(item.getLevel());
        //...
    }
}
```

###Retrieving the schema
```java
TF2Template dao = new TF2Template("API_KEY_HERE");
TF2Schema schema = dao.getSchema();
if (Status.SUCCESS.equals(schema.getStatus())){
    List<TF2SchemaQuality> qualities = schema.getActiveQualityMap();
}
```

###Spring JavaConfig Usage
```java
@Configuration
public class SteamDaoConfig {

    @Value("${steam.key}")
    private String key;

    @Bean
    public TF2Template steamDao() {
        boolean autoRetry = true;
        TF2Template steamDao = new TF2Template(key, autoRetry);
        Limiter limiter = new SimpleLimiter(100000,Duration.standardDays(1));
        steamDao.setLimiter(limiter);
        return steamDao;
    }
}
```

##Performance Highlights
 - Deserialize a full backpack containing 2000 items in under *700 nanoseconds*.
 - Over 1420 full size backpacks per second per thread.
 
##Legal
 Steam4j and solong.co.uk are unaffiliated with steampowered.com. Any trademarks such as Team Fortress 2, Steam, Valve are copyright of their respective owners.
