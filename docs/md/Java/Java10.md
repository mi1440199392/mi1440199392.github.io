```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.2</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.hundsun</groupId>
    <artifactId>elastic-search</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>elastic-search</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>7.13.2</version>
        </dependency>


        <!--ES高级客户端-->
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.13.2</version>
        </dependency>

        <!--Json工具-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.79</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

```

```java

package com.hundsun.elasticsearch;

import com.alibaba.fastjson.JSON;
import com.hundsun.elasticsearch.domain.User;
import org.apache.http.HttpHost;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.search.SearchRequest;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.CreateIndexResponse;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;
import org.elasticsearch.common.unit.Fuzziness;
import org.elasticsearch.common.xcontent.XContentType;
import org.elasticsearch.index.query.BoolQueryBuilder;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.index.query.RangeQueryBuilder;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.builder.SearchSourceBuilder;
import org.elasticsearch.search.fetch.subphase.highlight.HighlightBuilder;
import org.elasticsearch.search.sort.SortOrder;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import java.io.IOException;

@SpringBootTest
class ElasticSearchApplicationTests {


    /**
     * 创建索引
     *
     * @throws IOException
     */
    @Test
    void createIndex() throws IOException {

        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 创建索引
        CreateIndexRequest request = new CreateIndexRequest("mkm");
        CreateIndexResponse response = esClient.indices().create(request, RequestOptions.DEFAULT);
        System.out.println(response.index()); // 创建的索引名
        System.out.println(response.isAcknowledged()); //是否创建索引成功

        // 关闭Es
        esClient.close();

    }

    /**
     * 查询索引
     *
     * @throws IOException
     */
    @Test
    void getIndex() throws IOException {

        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 查询索引
        GetIndexRequest request = new GetIndexRequest("mkm");
        GetIndexResponse response = esClient.indices().get(request, RequestOptions.DEFAULT);

        System.out.println(response.getAliases()); // 查询别名
        System.out.println(response.getMappings());//查询映射结构
        System.out.println(response.getSettings());//查询配置

        // 关闭Es
        esClient.close();

    }

    /**
     * 删除索引
     *
     * @throws IOException
     */
    @Test
    void deleteIndex() throws IOException {

        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        //删除索引
        DeleteIndexRequest request = new DeleteIndexRequest("mkm");
        AcknowledgedResponse response = esClient.indices().delete(request, RequestOptions.DEFAULT);
        System.out.println(response.isAcknowledged());//是否删除成功


        // 关闭Es
        esClient.close();

    }

    /**
     * 创建文档数据
     *
     * @throws IOException
     */
    @Test
    void createDocument() throws IOException {

        // 准备数据
        User user = new User();
        user.setMid(1001L);
        user.setMName("张三");
        user.setMAge(100);
        user.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");


        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 将数据转为Json字符串，插入数据
        IndexRequest indexRequest = new IndexRequest();
        indexRequest.index("mkm").id("mkm-" + user.getMid()); // 创建id
        indexRequest.source(JSON.toJSONString(user), XContentType.JSON);

        IndexResponse response = esClient.index(indexRequest, RequestOptions.DEFAULT);
        System.out.println(response.getResult());//结果

        // 关闭Es
        esClient.close();

    }

    /**
     * 修改文档数据
     *
     * @throws IOException
     */
    @Test
    void updateDocument() throws IOException {

        // 准备数据
        User user = new User();
        user.setMid(1001L);
        user.setMName("李四");
        user.setMAge(200);
        user.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");


        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 将数据转为Json字符串，修改数据

        UpdateRequest updateRequest = new UpdateRequest();
        updateRequest.index("mkm").id("mkm-" + user.getMid());
        updateRequest.doc(JSON.toJSONString(user), XContentType.JSON);

        UpdateResponse response = esClient.update(updateRequest, RequestOptions.DEFAULT);
        System.out.println(response.getResult());//结果
        System.out.println(response.getIndex()); //索引
        System.out.println(response.getId());//id

        // 关闭Es
        esClient.close();

    }


    /**
     * 查询文档数据
     *
     * @throws IOException
     */
    @Test
    void getDocument() throws IOException {

        // 准备数据
        User user = new User();
        user.setMid(1001L);
        user.setMName("李四");
        user.setMAge(200);
        user.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 查询文档数据
        GetRequest getRequest = new GetRequest();
        getRequest.index("mkm").id("mkm-" + user.getMid());

        GetResponse response = esClient.get(getRequest, RequestOptions.DEFAULT);
        System.out.println(response.getSourceAsString()); //文档数据【字符串】
        System.out.println(response.getSourceAsMap()); //文档数据【Map结构】
        System.out.println(response.getIndex());//索引
        System.out.println(response.getId());//id

        // 关闭Es
        esClient.close();

    }


    /**
     * 删除文档数据
     *
     * @throws IOException
     */
    @Test
    void deleteDocument() throws IOException {

        // 准备数据
        User user = new User();
        user.setMid(1001L);
        user.setMName("李四");
        user.setMAge(200);
        user.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        // 创建ES高级客户端

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        //删除文档数据
        DeleteRequest deleteRequest = new DeleteRequest();
        deleteRequest.index("mkm").id("mkm-" + user.getMid());
        DeleteResponse response = esClient.delete(deleteRequest, RequestOptions.DEFAULT);
        System.out.println(response.toString());


        // 关闭Es
        esClient.close();

    }

    /**
     * 批量创建文档数据
     *
     * @throws IOException
     */

    @Test
    void batchCreateDocument() throws IOException {

        // 准备数据
        User user1 = new User();
        user1.setMid(1002L);
        user1.setMName("王五");
        user1.setMAge(200);
        user1.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user2 = new User();
        user2.setMid(1003L);
        user2.setMName("赵六");
        user2.setMAge(300);
        user2.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user3 = new User();
        user3.setMid(1004L);
        user3.setMName("田七");
        user3.setMAge(400);
        user3.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user4 = new User();
        user4.setMid(1005L);
        user4.setMName("张三");
        user4.setMAge(500);
        user4.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        //批量创建文档数据
        BulkRequest bulkRequest = new BulkRequest();

        bulkRequest.add(new IndexRequest().index("mkm").id("mkm-" + user1.getMid()).source(JSON.toJSONString(user1), XContentType.JSON));
        bulkRequest.add(new IndexRequest().index("mkm").id("mkm-" + user2.getMid()).source(JSON.toJSONString(user2), XContentType.JSON));
        bulkRequest.add(new IndexRequest().index("mkm").id("mkm-" + user3.getMid()).source(JSON.toJSONString(user3), XContentType.JSON));
        bulkRequest.add(new IndexRequest().index("mkm").id("mkm-" + user4.getMid()).source(JSON.toJSONString(user4), XContentType.JSON));

        BulkResponse response = esClient.bulk(bulkRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时


        // 关闭Es
        esClient.close();

    }

    /**
     * 批量修改文档数据
     *
     * @throws IOException
     */
    @Test
    void batchUpdateDocument() throws IOException {

        // 准备数据
        User user1 = new User();
        user1.setMid(1002L);
        user1.setMName("王五Copy");
        user1.setMAge(200);
        user1.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user2 = new User();
        user2.setMid(1003L);
        user2.setMName("赵六Copy");
        user2.setMAge(300);
        user2.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user3 = new User();
        user3.setMid(1004L);
        user3.setMName("田七Copy");
        user3.setMAge(400);
        user3.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user4 = new User();
        user4.setMid(1005L);
        user4.setMName("张三Copy");
        user4.setMAge(500);
        user4.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        //批量修改文档数据
        BulkRequest bulkRequest = new BulkRequest();
        bulkRequest.add(new UpdateRequest().index("mkm").id("mkm-" + user1.getMid()).doc(JSON.toJSONString(user1), XContentType.JSON));
        bulkRequest.add(new UpdateRequest().index("mkm").id("mkm-" + user2.getMid()).doc(JSON.toJSONString(user2), XContentType.JSON));
        bulkRequest.add(new UpdateRequest().index("mkm").id("mkm-" + user3.getMid()).doc(JSON.toJSONString(user3), XContentType.JSON));
        bulkRequest.add(new UpdateRequest().index("mkm").id("mkm-" + user4.getMid()).doc(JSON.toJSONString(user4), XContentType.JSON));

        BulkResponse response = esClient.bulk(bulkRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时


        // 关闭Es
        esClient.close();

    }

    /**
     * 批量删除文档数据
     *
     * @throws IOException
     */
    @Test
    void batchDeleteDocument() throws IOException {

        // 准备数据
        User user1 = new User();
        user1.setMid(1002L);
        user1.setMName("王五");
        user1.setMAge(200);
        user1.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user2 = new User();
        user2.setMid(1003L);
        user2.setMName("赵六");
        user2.setMAge(300);
        user2.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user3 = new User();
        user3.setMid(1004L);
        user3.setMName("田七");
        user3.setMAge(400);
        user3.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        User user4 = new User();
        user4.setMid(1005L);
        user4.setMName("张三");
        user4.setMAge(500);
        user4.setMAddress("杭州市滨江区江南大道1688号恒生电子股份有限公司");

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        //批量删除文档数据
        BulkRequest bulkRequest = new BulkRequest();

        bulkRequest.add(new DeleteRequest().index("mkm").id("mkm-" + user1.getMid()));
        bulkRequest.add(new DeleteRequest().index("mkm").id("mkm-" + user2.getMid()));
        bulkRequest.add(new DeleteRequest().index("mkm").id("mkm-" + user3.getMid()));
        bulkRequest.add(new DeleteRequest().index("mkm").id("mkm-" + user4.getMid()));

        BulkResponse response = esClient.bulk(bulkRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时


        // 关闭Es
        esClient.close();

    }

    /**
     * 高级查询【全量查询】
     *
     * @throws IOException
     */
    @Test
    void matchAllDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【全量查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");
        searchRequest.source(new SearchSourceBuilder().query(QueryBuilders.matchAllQuery()));

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits()); //总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }

    /**
     * 高级查询【单条件查询】
     *
     * @throws IOException
     */
    @Test
    void matchDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【条件查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        //searchRequest.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("mName","张")));            // 1.单条件查询

        //searchRequest.source(new SearchSourceBuilder().query(QueryBuilders.termsQuery("mName","张","王","田")));  // 2.单条件IN查询

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }

    /**
     * 高级查询【范围查询】
     *
     * @throws IOException
     */
    @Test
    void rangeQueryDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【条件查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("mAge");
        rangeQuery.gte(300); // 大于等于
        rangeQuery.lte(500); // 小于等于
        // rangeQuery.lt()   // 小于
        // rangeQuery.gt()   // 大于

        sourceBuilder.query(rangeQuery);
        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }


    /**
     * 高级查询【多条件查询--并且，或者】
     *
     * @throws IOException
     */
    @Test
    void boolQueryDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【条件查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        BoolQueryBuilder boolQuery = QueryBuilders.boolQuery(); //多条件查询
        // boolQuery.must(QueryBuilders.matchQuery("mName","张")); // 并且
        // boolQuery.must(QueryBuilders.matchQuery("mAddress","杭州")); // 并且
        // boolQuery.must(QueryBuilders.matchQuery("mAge",500)); // 并且


        boolQuery.should(QueryBuilders.matchQuery("mName", "张")); // 或者
        boolQuery.should(QueryBuilders.matchQuery("mid", 1002)); // 或者
        boolQuery.should(QueryBuilders.matchQuery("mAge", 300)); // 或者

        sourceBuilder.query(boolQuery);
        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }


    /**
     * 高级查询【模糊查询】
     *
     * @throws IOException
     */
    @Test
    void likeQueryDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【模糊查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.query(QueryBuilders.fuzzyQuery("mName", "Co").fuzziness(Fuzziness.TWO));
        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }

    /**
     * 高亮查询
     *
     * @throws IOException
     */
    @Test
    void highlightQueryDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询【模糊查询】
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");


        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

        sourceBuilder.query(QueryBuilders.termQuery("mName", "张"));

        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.preTags("<font color='red'>"); // 设置高亮标签
        highlightBuilder.postTags("</font>"); // 设置高亮标签
        highlightBuilder.field("mName");  // 设置高亮字段
        sourceBuilder.highlighter(highlightBuilder);

        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }

    /**
     * 分页排序
     *
     * @throws IOException
     */
    @Test
    void pageSortDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.query(QueryBuilders.matchAllQuery()); //设置查询条件
        sourceBuilder.from(2).size(2); // 设置分页
        sourceBuilder.sort("mAge", SortOrder.DESC);  //设置排序字段，降序
        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }

    /**
     * 过滤响应结果字段
     *
     * @throws IOException
     */
    @Test
    void filterDocument() throws IOException {

        // 创建ES高级客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        // 高级查询
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("mkm");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.query(QueryBuilders.matchAllQuery()); //设置查询条件
        String[] includes = {}; //设置结果包含字段
        String[] excludes = {"mid"}; //设置结果排除字段
        sourceBuilder.fetchSource(includes, excludes);
        searchRequest.source(sourceBuilder);

        SearchResponse response = esClient.search(searchRequest, RequestOptions.DEFAULT);
        System.out.println(response.getTook());//耗时
        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());//总条数
        for (SearchHit hit : hits.getHits()) {
            System.out.println(hit.getSourceAsString());//查询结果
        }


        // 关闭Es
        esClient.close();

    }
}

```