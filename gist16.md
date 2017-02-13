###[微博图床上传图片](https://segmentfault.com/q/1010000008250059)
```js
$cookie = 'your cookie';
$ch = curl_init('http://picupload.service.weibo.com/interface/pic_upload.php'
    . '?mime=image%2Fjpeg&data=base64&url=0&markpos=1&logo=&nick=0&marks=1&app=miniblog');

curl_setopt_array($ch, [
    CURLOPT_POST => true,
    CURLOPT_VERBOSE => true,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => ["Cookie: $cookie"],
    CURLOPT_POSTFIELDS => ['b64_data' => base64_encode(file_get_contents('./img.jpg'))],
]);

var_export(curl_exec($ch));

如果要用 http://picupload.service.weibo.com/interface/pic_upload.php?mime=image%2Fjpeg&data=base64&url=0&markpos=1&logo=&nick=0&marks=1&app=miniblog 这个 URL 的话
POST 参数必须是 b64_data，值为经过 base64 编码后的字符串

如果要使用 pic1 参数的话，则要用 multipart 方式进行上传，且 URL 中必须包含 cb 参数，cb 参数的值为 http://weibo.com/aj/static/upimgback.html?_wv=5&callback=STK_ijax_ 加(js)时间戳

2种方法相比较的话，我个人倾向于 multipart 方法，因为 base64 编码会使上传文件的体积增加 1/3，不仅上传时间要更久，流量消耗也多了 1/3

具体可看 http://js.t.sinajs.cn/t5/home... 或者
用 fiddler 抓包看下 http://weibo.com/minipublish 的上传过程

或者你可以用我造的轮子 PHP实现的微博图床上传轮子https://github.com/consatan/weibo_image_uploader，支持 PHP 5.5.9 以上版本

        $cookie="";
        $base64_image_content = base64_encode(file_get_contents('d:/gg.jpg'));
        $post_data['b64_data']=$base64_image_content;
        $header = array (); 
        $header [] = 'Content-Type:application/x-www-form-urlencoded';  
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, "http://picupload.service.weibo.com/interface/pic_upload.php?mime=image%2Fjpeg&data=base64&url=0&markpos=1&logo=&nick=0&marks=1&app=miniblog");
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_COOKIE, $cookie); 
        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($ch, CURLOPT_MAXREDIRS,20);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $header );  
        curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
        curl_setopt($ch, CURLINFO_HEADER_OUT, 1);
        curl_setopt($ch, CURLOPT_VERBOSE, 1);
        $output = curl_exec($ch);
        $headers = curl_getinfo($ch);
        print_r($headers);
        curl_close($ch);
        
        成功截图https://ooo.0o0.ooo/2017/02/10/589d505fa2481.png
        curl_setopt($ch, CURLOPT_COOKIEFILE, $cookie);
换成

$header [] = "Cookie:$cookie";  
curl_setopt($ch, CURLOPT_HTTPHEADER, $header );
```
###[python模拟登陆微博](https://github.com/ResolveWang/smart_login/blob/master/sina_login/sina_login_by_selenium.py)
```js
import time
from selenium import webdriver
import requests

# 该段代码在ubuntu上能成功运行，并没有在windows上面运行过
# 直接登陆新浪微博
url = 'http://weibo.com/login.php'
driver = webdriver.PhantomJS()
driver.get(url)
print('开始登陆')

# 定位到账号密码表单
login_tpye = driver.find_element_by_class_name('info_header').find_element_by_xpath('//a[2]')
login_tpye.click()
time.sleep(3)

name_field = driver.find_element_by_id('loginname')
name_field.clear()
name_field.send_keys('youraccount')

password_field = driver.find_element_by_class_name('password').find_element_by_name('password')
password_field.clear()
password_field.send_keys('yourpassword')

submit = driver.find_element_by_link_text('登录')
submit.click()

# 等待页面刷新，完成登陆
time.sleep(5)
print('登陆完成')
sina_cookies = driver.get_cookies()

cookie = [item["name"] + "=" + item["value"] for item in sina_cookies]
cookiestr = '; '.join(item for item in cookie)

# 验证cookie是否有效
redirect_url = 'http://weibo.com/p/1005051921017243/info?mod=pedit_more'
headers = {'cookie': cookiestr}
html = requests.get(redirect_url, headers=headers).text
print(html)


```
###[调用网页接口实现发微博（PHP实现）](http://andrewyang.cn/post.php?id=1034)
```js
//https://github.com/yangyuan/weibo-publisher
function rsa_encrypt($message, $e, $n) {
	$exponent = hex2bin($e);
	$modulus = hex2bin($n);
	$pkey = rsa_pkey($exponent, $modulus);
	openssl_public_encrypt($message, $result, $pkey, OPENSSL_PKCS1_PADDING);
	return $result;
}
$message=$servertime."\t".$nonce."\n".$password;
function weibo_get_image_url($pid) {
	$zone = 0;
	$pid_zone = crc32 ($pid);
	$type = 'large'; //bmiddle
	if ($pid[9] == 'w') {
		$zone = ($pid_zone & 3) + 1;
		$ext = ($pid[21] == 'g') ? 'gif' : 'jpg';
		$url = 'http://ww'.$zone.'.sinaimg.cn/'.$type.'/'.$pid.'.'.$ext;
	} else {
		$zone = (hexdec(substr($pid, -2)) & 0xf) + 1;
		$url = 'http://ss'.$zone.'.sinaimg.cn/'.$type.'/'.$pid.'&690';
	}
	return $url;
}
```
###[windows上使用pyvenv进行项目隔离](http://www.jianshu.com/p/d01a85c8995e)
python -m venv test 从python3.4开始，就已经自带了pyvenv，我使用的是python3.5。如果版本低于3.4，可以使用pip安装virtualenv这个库，它们用法基本一致。
cd test/Scripts

activate
deactivate
在A环境中把所有依赖都保存到re.txt中,使用pip freeze:

pip freeze > re.txt
这时会在当前目录生成re.txt,通过记事本可以直接打开:

notepad re.txt
可以看到类似内容:

Flask==0.11.1
Flask-Login==0.3.2
Flask-SQLAlchemy==2.1
我们可以修改该文件来改变我们虚拟环境的相关依赖，比如我们不需要Flask,直接删除Flask==0.11.1即可。

这个文件怎么用呢,我们先激活B虚拟环境，然后可以一条命令安装所有依赖:

pip install -r re.txt
###[使用python+微博进行远程关机](http://www.jianshu.com/p/458a2656ca61)
```js
微博爬虫，使用requets+bs+oracle搭建的爬虫框架。包括对微博搜索、微博信息和用户资料的抓取、解析、存储和可视化展示https://github.com/ResolveWang/WeiboSpider
beautifulsoup我用得最多的是find(attrs={key: value})
 def get_newest(session, uid):
    # 获取只含有原创内容的个人主页
    url = 'http://weibo.com/' + uid + '/profile?profile_ftype=1&is_ori=1#_0'
    page = session.get(url).text
    soup = BeautifulSoup(page, 'html.parser')
    scripts = soup.find_all('script')
    status = ' '
    for s in scripts:
        if 'pl.content.homeFeed.index' in s.string:
                status = s.string
    #用正则表达式获取微博原创内容
    pattern = re.compile(r'FM.view((.*))')
    rs = pattern.search(status)
    if rs:
        cur_status = rs.group(1)
        html = json.loads(cur_status).get('html')
        soup = BeautifulSoup(html, 'html.parser')
        # 获取最新一条微博所有信息
        newest = soup.find(attrs={'action-type': 'feed_list_item'})
        # 获取最新发布内容
        post_cont = newest.find(attrs={'node-type': 'feed_list_content'}).text.strip()
        # 获取最新发布时间
        post_stamp = int(newest.find(attrs={'node-type': 'feed_list_item_date'}).get('date')[:-3])
        post_time = datetime.fromtimestamp(post_stamp)
        now = datetime.now()
        # 计算此刻和发布时间的时间差(单位为秒)
        t = (now - post_time).total_seconds()
        return post_cont, t
    else:
        return None
```