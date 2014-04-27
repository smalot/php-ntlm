php-ntlm
========

NTLM authentication for PHP

Standard Usage
=====

Standard use, without Samba, with stored password (eg: in database).

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

Active Directory
=========

In case of use with Active Directory, you won't be able to check password directly from PHP because password are stored securely into Active Directory.
You should implement your own logic like that :

````
/**
 *
 * @param string $challenge Challenge used for the handshake
 * @param string $user      User name
 * @param string $domain    Domain name
 * @param string $workstation Name of the workstation
 * @param string $clientblobhash
 * @param string $clientblob
 * @param string $get_ntlm_user_hash Callback function to retrieve user hash password
 */
function ntlm_verify_hash_activedirectory($challenge, $user, $domain, $workstation, $clientblobhash, $clientblob, $get_ntlm_user_hash) {
	$valid = true;

	// TODO: Your logic
	
	return $valid;
}

$auth = ntlm_prompt("testwebsite", "testdomain", "mycomputer", "testdomain.local", "mycomputer.local", null, "ntlm_verify_hash_activedirectory");
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


Errors
======

In case of "NTLMv2 response required. Please force your client to use NTLMv2." error, you have to authorize your browser to negotiate with NTLMv2 by removing "LMCompatibilityLevel" entry.

Open "regedit" and delete the following entry:

````
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\LMCompatibilityLevel
````

Documentation
-------------

english: http://technet.microsoft.com/en-us/library/cc960646.aspx
french: http://www.itpro.fr/a/la-protection-de-lmcompatibilitylevel/
