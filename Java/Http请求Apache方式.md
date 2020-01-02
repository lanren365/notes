# Http及HTTPS请求Apache方式（apache HttpClient 4.5.2）
## 对于HTTPS的访问，采取绕过证书的策略：
```java
package com.ssm.common;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.http.HttpEntity;
import org.apache.http.NameValuePair;
import org.apache.http.ParseException;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.config.Registry;
import org.apache.http.config.RegistryBuilder;
import org.apache.http.conn.socket.ConnectionSocketFactory;
import org.apache.http.conn.socket.PlainConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.security.KeyManagementException;
import java.security.NoSuchAlgorithmException;
import java.security.cert.CertificateException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

/**
 * @author wugong.jie
 * @version v1.0.0
 * @date 2018/3/9
 */
public class HttpClientUtils {

    private static Log log = LogFactory.getLog(HttpClientUtils.class);

    private final static String DEFAULT_ENCODING = "UTF-8";
    private final static int DEFAULT_CONNECT_TIMEOUT = 5000; // 设置连接超时时间，单位毫秒
    private final static int DEFAULT_CONNECTION_REQUEST_TIMEOUT = 1000;// 设置从connect Manager获取Connection 超时时间，单位毫秒
    private final static int DEFAULT_SOCKET_TIMEOUT = 5000;// 请求获取数据的超时时间，单位毫秒 如果访问一个接口，多少时间内无法返回数据，就直接放弃此次调用
    /**
     * 绕过验证
     *
     * @return
     * @throws NoSuchAlgorithmException
     * @throws KeyManagementException
     */
    public static SSLContext createIgnoreVerifySSL() throws NoSuchAlgorithmException, KeyManagementException {
        SSLContext sc = SSLContext.getInstance("SSLv3");

        // 实现一个X509TrustManager接口，用于绕过验证，不用修改里面的方法
        X509TrustManager trustManager = new X509TrustManager() {
            @Override
            public void checkClientTrusted(
                    java.security.cert.X509Certificate[] paramArrayOfX509Certificate,
                    String paramString) throws CertificateException {
            }

            @Override
            public void checkServerTrusted(
                    java.security.cert.X509Certificate[] paramArrayOfX509Certificate,
                    String paramString) throws CertificateException {
            }

            @Override
            public java.security.cert.X509Certificate[] getAcceptedIssuers() {
                return null;
            }
        };

        sc.init(null, new TrustManager[] { trustManager }, null);
        return sc;
    }

    /**
     * 模拟请求
     *
     * @param url        资源地址
     * @param map    参数列表
     * @param encoding    编码
     * @return
     * @throws NoSuchAlgorithmException
     * @throws KeyManagementException
     * @throws IOException
     * @throws ClientProtocolException
     */
    public static String sendSSLPost(String url, Map<String,String> map, String encoding) throws KeyManagementException, NoSuchAlgorithmException, ClientProtocolException, IOException {
        String body = "";
        CloseableHttpResponse response = null;
        try {
            //采用绕过验证的方式处理https请求
            SSLContext sslcontext = createIgnoreVerifySSL();
            // 设置协议http和https对应的处理socket链接工厂的对象
            Registry<ConnectionSocketFactory> socketFactoryRegistry = RegistryBuilder.<ConnectionSocketFactory>create()
                    .register("http", PlainConnectionSocketFactory.INSTANCE)
                    .register("https", new SSLConnectionSocketFactory(sslcontext))
                    .build();
            PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager(socketFactoryRegistry);
            HttpClients.custom().setConnectionManager(connManager);

            //创建自定义的httpclient对象
            CloseableHttpClient client = HttpClients.custom().setConnectionManager(connManager).build();
            //创建post方式请求对象
            HttpPost httpPost = new HttpPost(url);
            //装填参数
            httpPost = postForm(httpPost,map,DEFAULT_ENCODING);
            //执行请求操作，并拿到结果（同步阻塞）
            response = client.execute(httpPost);
            //获取结果实体
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                //按指定编码转换结果实体为String类型
                body = EntityUtils.toString(entity, encoding);
            }
            EntityUtils.consume(entity);
        }catch (UnsupportedEncodingException v1){
            v1.printStackTrace();
            log.error("IOException异常",v1);
        }catch (IOException v2) {
            v2.printStackTrace();
            log.error("IOException异常",v2);
        } catch (ParseException v3) {
            v3.printStackTrace();
            log.error("ParseException异常",v3);
        }finally {
            //释放链接
            response.close();
        }
        return body;
    }

    /**
     * 模拟请求
     *
     * @param url        资源地址
     * @param map    参数列表
     * @param encoding    编码
     * @return
     * @throws ParseException
     * @throws IOException
     */
    public static String send(String url, Map<String,String> map,String encoding) throws ParseException, IOException{
        String body = "";
        CloseableHttpResponse response = null;
        try {
            //创建httpclient对象
            CloseableHttpClient client = HttpClients.createDefault();
            //创建post方式请求对象
            HttpPost httpPost = new HttpPost(url);
            httpPost = postForm(httpPost,map,encoding);
            //执行请求操作，并拿到结果（同步阻塞）
            response = client.execute(httpPost);
            //获取结果实体
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                //按指定编码转换结果实体为String类型
                body = EntityUtils.toString(entity, encoding);
            }
            EntityUtils.consume(entity);
        }catch (UnsupportedEncodingException v1){
            v1.printStackTrace();
            log.error("IOException异常",v1);
        }catch (IOException v2) {
            v2.printStackTrace();
            log.error("IOException异常",v2);
        } catch (ParseException v3) {
            v3.printStackTrace();
            log.error("ParseException异常",v3);
        }finally {
            //释放链接
            response.close();
        }
        return body;
    }


    private static HttpPost postForm(HttpPost httpPost,Map<String,String> map,String encoding) throws UnsupportedEncodingException {
        //装填参数
        List<NameValuePair> nvps = new ArrayList<NameValuePair>();
        if(map!=null){
            for (Map.Entry<String, String> entry : map.entrySet()) {
                nvps.add(new BasicNameValuePair(entry.getKey(), entry.getValue()));
            }
        }
        //设置参数到请求对象中
        httpPost.setEntity(new UrlEncodedFormEntity(nvps, encoding));
        //设置header信息
        //指定报文头【Content-type】、【User-Agent】
        httpPost.setHeader("Content-type", "application/x-www-form-urlencoded");
        httpPost.setHeader("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");
        RequestConfig requestConfig = RequestConfig.custom()
                .setConnectTimeout(DEFAULT_CONNECT_TIMEOUT).setConnectionRequestTimeout(DEFAULT_CONNECTION_REQUEST_TIMEOUT)
                .setSocketTimeout(DEFAULT_SOCKET_TIMEOUT).build();
        httpPost.setConfig(requestConfig);
        return httpPost;
    }

}
```
测试代码：
```java
package com.ssm.demo;

import com.ssm.common.HttpClientUtils;
import org.junit.Test;

import java.io.IOException;
import java.security.KeyManagementException;
import java.security.NoSuchAlgorithmException;

/**
 * @author wugong.jie
 * @version v1.0.0
 * @date 2018/3/9
 */
public class HttpClientTest {

    @Test
    public void httpsClientTest() throws NoSuchAlgorithmException, KeyManagementException, IOException {
        // 我现在还没确定到底是否正确访问https
        System.out.println(HttpClientUtils.sendSSLPost("https://my.oschina.net/wugong/blog",null,"UTF-8"));
    }

    @Test
    public void httpClientTest() throws IOException {
        System.out.println(HttpClientUtils.send("https://my.oschina.net/wugong/blog",null,"UTF-8"));
    }

}
```
但是，如果是自己用jdk或者其他工具生成的证书，还是希望用其他方式认证自签名的证书，这篇文章就来分享一下如何设置信任自签名的证书。当然你也可以参考官网示例中
## 使用签名证书
要想信任自签名的证书，必须得知道密钥库的路径及密钥库的密码。然后加载到程序来才可以。具体代码如下：
```java
private static String keyStorePath = "D:\\keys\\wsriakey";
private static String keyStorepass = "tomcat";
```

```java
/**
 * 设置信任自签名证书
 *
 * @param keyStorePath        密钥库路径
 * @param keyStorepass        密钥库密码
 * @return
 */
public static SSLContext custom(String keyStorePath, String keyStorepass){
    SSLContext sc = null;
    FileInputStream instream = null;
    KeyStore trustStore = null;
    try {
        trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
        instream = new FileInputStream(new File(keyStorePath));
        trustStore.load(instream, keyStorepass.toCharArray());
        // 相信自己的CA和所有自签名的证书
        sc = SSLContexts.custom().loadTrustMaterial(trustStore, new TrustSelfSignedStrategy()).build();
    } catch (KeyStoreException | NoSuchAlgorithmException| CertificateException | IOException | KeyManagementException e) {
        e.printStackTrace();
    } finally {
        try {
            instream.close();
        } catch (IOException e) {
        }
    }
    return sc;
}

/**
 * 模拟请求
 *
 * @param url        资源地址
 * @param map    参数列表
 * @param encoding    编码
 * @return
 * @throws ParseException
 * @throws IOException
 * @throws KeyManagementException
 * @throws NoSuchAlgorithmException
 * @throws ClientProtocolException
 */
public static String sendSSLPostCert(String url, Map<String,String> map,String encoding) throws ClientProtocolException, IOException {
    String body = "";

    //tomcat是我自己的密钥库的密码，你可以替换成自己的
    //如果密码为空，则用"nopassword"代替
    SSLContext sslcontext = custom(keyStorePath, keyStorepass);

    // 设置协议http和https对应的处理socket链接工厂的对象
    Registry<ConnectionSocketFactory> socketFactoryRegistry = RegistryBuilder.<ConnectionSocketFactory>create()
            .register("http", PlainConnectionSocketFactory.INSTANCE)
            .register("https", new SSLConnectionSocketFactory(sslcontext))
            .build();
    PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager(socketFactoryRegistry);
    HttpClients.custom().setConnectionManager(connManager);

    //创建自定义的httpclient对象
    CloseableHttpClient client = HttpClients.custom().setConnectionManager(connManager).build();

    //创建post方式请求对象
    HttpPost httpPost = new HttpPost(url);

    //装填参数
    List<NameValuePair> nvps = new ArrayList<NameValuePair>();
    if(map!=null){
        for (Map.Entry<String, String> entry : map.entrySet()) {
            nvps.add(new BasicNameValuePair(entry.getKey(), entry.getValue()));
        }
    }
    //设置参数到请求对象中
    httpPost.setEntity(new UrlEncodedFormEntity(nvps, encoding));

    System.out.println("请求地址："+url);
    System.out.println("请求参数："+nvps.toString());

    //设置header信息
    //指定报文头【Content-type】、【User-Agent】
    httpPost.setHeader("Content-type", "application/x-www-form-urlencoded");
    httpPost.setHeader("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");

    //执行请求操作，并拿到结果（同步阻塞）
    CloseableHttpResponse response = client.execute(httpPost);
    //获取结果实体
    HttpEntity entity = response.getEntity();
    if (entity != null) {
        //按指定编码转换结果实体为String类型
        body = EntityUtils.toString(entity, encoding);
    }
    EntityUtils.consume(entity);
    //释放链接
    response.close();
    return body;
}
```
