php-ntlm
========

NTLM authentication for PHP

Usage
=====

````
	include('ntlm.php');

	function get_ntlm_user_hash($user) {
		$userdb = array('loune'=>'test', 'user1'=>'password');
		
		if (!isset($userdb[strtolower($user)]))
			return false;	
		return ntlm_md4(ntlm_utf8_to_utf16le($userdb[strtolower($user)]));
	}

	session_start();
	$auth = ntlm_prompt("testwebsite", "testdomain", "mycomputer", "testdomain.local", "mycomputer.local", "get_ntlm_user_hash");

	if ($auth['authenticated']) {
		print "You are authenticated as $auth[username] from $auth[domain]/$auth[workstation]";
	}
````

To logout, use the code:

````
	ntlm_unset_auth();
````
	
SAMBA
=====

To use this library with samba, please read the instructions inside verifyntlm.c 
to compile the verifyntlm helper. Use the ntlm.php library as above but omit the
get_ntlm_user_hash function and replace the ntlm_prompt line with this one:

````
	$auth = ntlm_prompt("testwebsite", "testdomain", "mycomputer", "testdomain.local", "mycomputer.local", null, "ntlm_verify_hash_smb");
````
	
For more, see http://siphon9.net/loune/2010/12/php-ntlm-integration-with-samba/
